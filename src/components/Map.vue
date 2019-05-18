<template lang="pug">
    #map
</template>

<script>
import mapboxgl from 'mapbox-gl';
import U from 'mapbox-gl-utils';
import axios from 'axios';
import MapboxGeocoder from 'mapbox-gl-geocoder';
const d3 = require('d3-dsv');


function csvToGeoJSON(rows) {
    return {
        type: 'FeatureCollection',
        features: rows.map(row => ({
            type: 'Feature',
            geometry: {
                type: 'Point',
                coordinates: [row.Lng, row.Lat]
            },
            properties: row
        }))
    };
}

const clinicLookup = {}

function processData() {
    window.Map.clinics.forEach(clinic => {
        clinicLookup[clinic.ID] = clinic;
        clinic.doctors = [];

    });
    window.Map.doctors.forEach(doctor => {
        doctor.Clinics.split(/[^0-9]+/).forEach(clinicID => {
            const clinic = clinicLookup[clinicID];
            if (!clinic) {
                if (clinicID) {
                    console.error("No clinic with ID ", clinicID);
                }
                return
            }
            clinic.doctors.push(doctor);
        });
    });
    for (const clinicID of Object.keys(clinicLookup)) {
        const clinic = clinicLookup[clinicID];
        clinic.L = !!clinic.doctors.find(d => d.Lesbians);
        clinic.G = !!clinic.doctors.find(d => d['Gay men']);
        clinic.B = !!clinic.doctors.find(d => d['Bisexual women']);
        clinic.T = !!clinic.doctors.find(d => d['Trans people']);

        clinic.codesText = '';
        const codes = ['L','G','B','T'];
        for (const code of codes) {
            if (clinic[code]) {
                clinic.codesText += code;
            }
        }
    }
}

export default {
    mounted() {
        mapboxgl.accessToken = 'pk.eyJ1Ijoic3RldmFnZSIsImEiOiJGcW03aExzIn0.QUkUmTGIO3gGt83HiRIjQw';
        const map = new mapboxgl.Map({
            container: 'map',
            center: [145, -37.8],
            zoom: 12,
            style: 'mapbox://styles/mapbox/light-v9',
        });
        U.init(map);
        map.U.loadImage('clinic-marker', '/first-aid-kit.png');

        map.addControl(new MapboxGeocoder({
            accessToken: mapboxgl.accessToken,
            mapboxgl: mapboxgl,
            countries: 'au'
        }));

        window.map = map;
        window.Map = this;

        axios.get('http://emscycletours.site44.com/QueerDoc/clinics.csv')
        .then(csv => {
            this.clinics = d3.csvParse(csv.data);
            // this.clinics = csvToGeoJSON(d3.csvParse(csv.data));
        }).then(() => {
            // TODO load non-sequentially
            return axios.get('http://emscycletours.site44.com/QueerDoc/doctors.csv')
        }).then(csv =>  {
            this.doctors = d3.csvParse(csv.data);
            processData();
            map.U.onLoad(() => initMap(map));

        });
    }, methods: {
        updateFilter(filter) {
            const f = [];
            for (const field of ['L','G','B','T']) {
                if (filter[field]) {
                    f.push(['==', ['get', field], true]);
                }
            }
            map.U.setFilter(['clinic-points', 'clinic-circle', 'clinic-code'], ['all', ...f]);
        }
    }
}

function initMap(map) {
    let popup = new mapboxgl.Popup({
        closeButton: false,
        closeOnClick: false
    });
    map.U.addGeoJSON('clinics', csvToGeoJSON(Object.keys(clinicLookup).map(k => clinicLookup[k])));
    // map.U.addCircle('clinic-circle', 'clinics', {
    //     circleColor: 'red'
    // });
    map.U.addSymbol('clinic-points', 'clinics', {
        iconImage: 'clinic-marker',
        iconSize: 0.05,
        iconAllowOverlap: true
    });
    map.U.addSymbol('clinic-code', 'clinics', {
        textField: '{codesText}',
        textAllowOverlap: true,
        textOffset: [0,1],
        textHaloWidth: 2,
        textHaloColor: 'white',
        minzoom: 12
    });
    map.U.hoverPointer('clinic-points');
    map.on('click', 'clinic-points', e => {
        const clinic = clinicLookup[e.features[0].properties.ID];
        window.ClinicInfo.clinic = clinic;
        window.ClinicInfo.doctors = clinic.doctors;
    });
    map.on('mouseenter', 'clinic-points', e => {
        popup.setLngLat(e.features[0].geometry.coordinates)
            .setHTML(e.features[0].properties['Name of clinic'])
            .addTo(map);
    });
    map.on('mouseleave', 'clinic-points', e => {
        popup.remove();
    });
}

import 'mapbox-gl/dist/mapbox-gl.css';
import 'mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css';

</script>

<style scoped>
#map {
    width: 100%;
    height: 100%;
    position:absolute;
}
</style>