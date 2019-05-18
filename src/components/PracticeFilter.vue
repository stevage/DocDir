<template lang="pug">
#PracticeFilter
    | Find me a doctor: 
    br
    .code
        input#check-L(type="checkbox" v-model="filter.L" @change="change")
        label.L(for="check-L") L
    .code
        input#check-G(type="checkbox" v-model="filter.G" @change="change")
        label.G(for="check-G") G
    .code
        input#check-B(type="checkbox" v-model="filter.B" @change="change")
        label.B(for="check-B") B
    .code
        input#check-T(type="checkbox" v-model="filter.T" @change="change")
        label.T(for="check-T") T

</template>

<script>
export default {
    name: "PracticeFilter",
    data: () => ({
        filter: {}
        
    }),
    created() {
        window.PracticeFilter = this;
    }, watch: {
        filter() {
            // console.log(this.filter);
        }
    }, methods: {
        change() {
            console.log(this.filter);
            window.Map.updateFilter(this.filter);
        }
    }
}
</script>

<style scoped>
#PracticeFilter {
    padding: 1em;
}

.code {
    display: inline-block;
    margin: 5px;
}

label {
    display: inline-block;
    border: 1px solid hsl(0,0%,30%);
    border-radius: 15px;
    /* padding: 5px 10px; */
    width: 30px;
    /* height: 30px; */
    padding: 5px 0;
    text-align: center;
    /* vertical-align: center; */
    
    
}

input[type="checkbox"] {
    display: none;
}

input[type="checkbox"]:checked + label.L{
    background: hsl(0,80%,70%);
    /* color: white; */
}
input[type="checkbox"]:checked + label.G{
    background: hsl(30,80%,70%);
    /* color: white; */
}
input[type="checkbox"]:checked + label.B{
    background: hsl(60,80%,70%);
    /* color: white; */
}
input[type="checkbox"]:checked + label.T{
    background: hsl(110,70%,60%);
    /* color: white; */
}



</style>