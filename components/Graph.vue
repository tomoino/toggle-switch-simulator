<template>
    <div class="small">
        <line-chart :chart-data="datacollection" :options="values.options"></line-chart>
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
        // this.datacollection = {
        //   labels: [this.getRandomInt(), this.getRandomInt()],
        //   datasets: [
        //     {
        //       label: 'Test Data2',
        //       backgroundColor: 'rgba(255, 100, 130, 0.2)',
        //       data: [this.getRandomInt(), this.getRandomInt(), this.getRandomInt()]
        //     }, {
        //       label: 'Test Data2',
        //       backgroundColor: 'rgba(100, 130, 255, 0.2)',
        //       data: [this.getRandomInt(), this.getRandomInt(), this.getRandomInt()]
        //     }
        //     , {
        //       label: 'Test Data3',
        //       backgroundColor: 'rgba(130, 255, 100, 0.2)',
        //       data: [this.getRandomInt(), this.getRandomInt(), this.getRandomInt()]
        //     }
        //   ]
        // }
      }
    }
};
// import { ChartData, ChartOptions } from "chart.js";
// import ChartLine from "~/components/ChartLine.vue";
// import ChartBar from "~/components/ChartBar.vue";

// export default {
//   components: {
//     ChartLine,
//     ChartBar
//   },
//   props: {
//     values: {
//       type: Object,
//       default: null
//     }
//   },
//   data: () => {
//     return {
//         chartOptions: {
//             maintainAspectRatio: false
//         },
//         chartStyles: {
//             height: "100%",
//             width: "100%"
//         }
//     }
//   }
// }
</script>

<style>
  .small {
    max-width: 600px;
    margin:  150px auto;
  }
</style>