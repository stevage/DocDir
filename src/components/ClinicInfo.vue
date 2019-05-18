<template lang="pug">
#ClinicInfo(v-if="clinic['Name of clinic']")
    .name {{ clinic['Name of clinic'] }}
    .code.L(v-if="clinic.L") L
    .code.G(v-if="clinic.G") G
    .code.B(v-if="clinic.B") B
    .code.T(v-if="clinic.T") T
    table
        tr
            th Phone
            td {{ clinic['Phone'] }}
        tr
            th Address
            td 
                | {{ clinic['Street address'] }}
                br
                | {{ clinic['Suburb'] }}
        tr
            th Website
            td 
                a(:href="clinic['Website']") Visit


    h3 Doctors
    .doctor(v-for="doctor in doctors")
        .name {{ doctor.title }} {{ doctor['First Name'] }} {{ doctor['Last Name'] }}
            span(v-if="doctor.Gender")  ({{ doctor.Gender }})
        .type {{ doctor['Type of doctor'] }}
        .codes
            .code.L(v-if="doctor['Lesbians']") L
            .code.G(v-if="doctor['Gay men']") G
            .code.B(v-if="doctor['Bisexual women']") B
            .code.T(v-if="doctor['Trans people']") T
</template>

<script>
/*
Title
First Name
Last Name
Source
Gender
Type of doctor
Languages
Lesbians
Trans people
Gay men
Bisexual women
Sex workers
Clinics
*/
/*
ID
Name of clinic
Street address
Suburb
Post code
State
Phone
Email
Website
Bulk billing
Notes
Lat
Lng
*/
export default {
    name: "ClinicInfo",
    data: () => ({
        clinic: {},
        doctors: []
    }),
    created() {
        window.ClinicInfo = this;
    }
}
</script>

<style scoped>
#ClinicInfo {
    margin-top: 20px;
    border-top: 1px solid lightgrey;
}

table {
    margin: 1em 0;
    padding: 0 1em;
    border: 1px solid lightgrey;

}
td,td {
    padding: 0 0.5em;
}

td {
    color: #444;
}

th {
    vertical-align: top;
}

.doctor {
    padding-left: 1em;
    margin-bottom: 1em;
    background: hsl(0,0%,97%);
}

.name {
    font-weight: bold;
}

.type {
    color: #444;
}

.code {
    display: inline-block;
    border: 1px solid hsl(0,0%,30%);
    border-radius: 15px;
    padding: 5px 10px;
}

.L{
    background: hsl(0,80%,70%);
    /* color: white; */
}
.G{
    background: hsl(30,80%,70%);
    /* color: white; */
}
.B{
    background: hsl(60,80%,70%);
    /* color: white; */
}
.T{
    background: hsl(110,70%,60%);
    /* color: white; */
}



</style>