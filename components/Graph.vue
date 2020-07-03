<template>
    <div>
        <line-chart :chart-data="datacollection" :options="values.options"></line-chart>
        <slot/>
    </div>
</template>
 
<script>
export default {
    data () {
      return {
        datacollection: {}
      }
    },
    props: {
      values: {
        type: Object,
        default: null
      }
    },
    mounted () {
      this.fillData()
    },
    watch: {
      values: function(){
        this.fillData();
      }
    },
    methods: {
      fillData () {
        this.datacollection = {
          labels: this.values.x,
          datasets: this.values.y
        }
      }
    }
};
</script>
