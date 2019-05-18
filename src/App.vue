<template lang="pug">
    #app
        #top
            h1 DocDir
        #middle
            #sidebar(v-show="!collapsed")
                #toggle-buffer
                PracticeFilter
                ClinicInfo
            #map-container
                Map
            button#toggle(@click="toggle") Toggle sidebar
        //- #bottom
</template>

<script>
import Map from './components/Map.vue'
import PracticeFilter from './components/PracticeFilter.vue'
import ClinicInfo from './components/ClinicInfo.vue'

export default {
    name: 'app',
    components: {
      Map,
      PracticeFilter,
      ClinicInfo
    }, data: () => ({
        collapsed: false
    }), methods: {
        toggle() {
            this.collapsed = !this.collapsed;
            this.$nextTick(() => window.map.resize());
        }
    }
}
</script>

<style>
html, body {
  height: 100vh;
  width: 100%;
  margin:0;
  padding:0;
}

#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    display: flex;
    flex-direction: column;
    height:100vh;
}

#top {
    flex-basis: 40px;
    padding: 0 1em;
    border-bottom: 1px solid grey;
    background:hsl(30, 43.5%, 91%);    
}

#middle {
    flex-grow: 1;
    min-height: 0; /* Crucial to making overflow scroll work */
    display: flex;
}

#sidebar {
    flex-basis: 300px; /* Horizontally */
    border-right: 1px solid lightgrey;
    overflow: scroll;
}

#map-container {
    flex-grow:1;
    flex-basis:400px;
    position:relative;
}

#bottom {
    flex-basis: 50px;
    border-top: 1px solid lightgrey;
}

#toggle {
    display: none;
}

@media (max-width: 500px) { 
    #top {
        display: none;
    }

    #toggle-buffer {
        height: 30px;
    }
 
    #toggle {
        display:block;
        position: absolute;
        top: 0px;
        width: 120px;
        height: 30px;
        background: hsl(0,0%,95%);
        border: none;
        /* padding: 1em; */
    }
}
</style>
