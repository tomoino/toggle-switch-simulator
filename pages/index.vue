<template>
  <div class="container">
    <div>
      <h1 class="title">
        Toggle Switch Simulator
      </h1>
      <vs-row>
        <vs-col vs-type="flex" vs-justify="center" vs-align="center" vs-w="6">
          <Graph :values="firstGraphData"/>
        </vs-col>
        <vs-col vs-type="flex" vs-justify="center" vs-align="center" vs-w="6">
          <Graph :values="secondGraphData"/>
        </vs-col> 
      </vs-row>
      <div>
        <h3>U0 = {{Math.floor(u0*100)/100}}</h3><vs-slider v-model="u0Slider"/>
        <h3>V0 = {{Math.floor(v0*100)/100}}</h3><vs-slider v-model="v0Slider"/>
        <h3>I1 = {{Math.floor(I1*100)/100}}</h3><vs-slider :max="200" v-model="I1Slider"/>
        <h3>I2 = {{Math.floor(I2*100)/100}}</h3><vs-slider :max="200" v-model="I2Slider"/>
      </div>    
    </div>
  </div>
</template>

<script>
import Graph from '~/components/Graph.vue'

export default {
  components: {
    Graph
  },
  data () {
    return {
      u0Slider: 0,
      v0Slider: 0,
      I1Slider: 100,
      I2Slider: 100,
    }
  },
  methods: {
    calcConcentration ( u0, v0, I1, I2) {
      const [t0, tmax, division] = [0, 30, 100]
      let U = [], V = [], T = []
      let [u, v, t] = [u0, v0, t0]

      const [a, n] = [1, 8]
      const [b, m] = [1, 8]

      let dudt = (_u, _v) =>  (a / (1 + Math.pow(_v, n))) - _u + I1
      let dvdt = (_u, _v) =>  (b / (1 + Math.pow(_u, m))) - _v + I2
      const dt = (tmax-t0) / division
      
      for ( let i = 0; i <= division; i++) {
        u += dt * dudt(u, v)
        v += dt * dvdt(u, v)
        t += dt
        U.push(u)
        V.push(v)
        T.push(Math.floor(t*100)/100)
      }

      return {
        x:T,
        y:[
          {
            label: 'u',
            backgroundColor: 'rgba(255, 100, 130, 0.2)',
            data: U
          },
          {
            label: 'v',
            backgroundColor: 'rgba(100, 130, 255, 0.2)',
            data: V
          }
        ]
      }
    },
    calcNullcline ( I1, I2) {
      let U = [], V_of_nullU = [], V_of_nullV = []

      const [a, n] = [1, 8]
      const [b, m] = [1, 8]

      const nullUMax = I1 + a 
      const nullVMax = I2 + b

      let calc_u_of_nullU = (_v) =>  (a / (1 + Math.pow(_v, n))) + I1
      let calc_v_of_nullU = (_u) =>  Math.pow(a/(_u - I1) - 1, 1/n)
      let calc_v_of_nullV = (_u) =>  (b / (1 + Math.pow(_u, m))) + I2
      const du = 0.01

      for (let u = 0; u <= nullUMax; u += du) {
        let v = calc_v_of_nullV(u)
        let v_of_nullU = calc_v_of_nullU(u)
        V_of_nullV.push(v)
        V_of_nullU.push(v_of_nullU)
        U.push(Math.floor(u*100)/100)
      }

      return {
        x:U,
        y:[
          {
            label: 'nullcline dudt=0',
            backgroundColor: 'rgba(255, 100, 130, 0.2)',
            data: V_of_nullU
          },
          {
            label: 'nullcline dvdt=0',
            backgroundColor: 'rgba(100, 130, 255, 0.2)',
            data: V_of_nullV
          }
        ],
        options: {
          scales: {
            xAxes: [
              {
                ticks: {
                  beginAtZero: true,
                  min: 0,
                  max: nullUMax
                }
              }
            ],
            yAxes: [
              {
                ticks: {
                  beginAtZero: true,
                  min: 0,
                  max: nullVMax
                }
              }
            ]
          }
        }
      }
    }
  },
  computed: {
    firstGraphData: function() {
      return this.calcConcentration(this.u0,this.v0,this.I1,this.I2)
    },
    secondGraphData: function() {
      return this.calcNullcline(this.I1,this.I2)
    },
    u0: function(){
      return this.u0Slider*0.01
    },
    v0: function(){
      return this.v0Slider*0.01
    },
    I1: function(){
      return this.I1Slider*0.01
    },
    I2: function(){
      return this.I2Slider*0.01
    },
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 55px;
  color: #35495e;
  letter-spacing: 1px;
  text-transform: capitalize;
  margin: 25px 0px
}

.subtitle {
  font-weight: 300;
  font-size: 1.1rem;
  color: #526488;
  word-spacing: 2px;
  padding-bottom: 15px;
  max-width: 600px;
}

.subtitle a {
  font-weight: 500;
  color: inherit
}

.links {
  padding-top: 15px;
  margin-bottom: 20px
}

.content-logos {
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 500px;
}
.plus {
  font-size: 2.5rem;
  margin: 15px;
  color: #35495e
}
.h3 {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-weight: 400;
  margin: 10px;
}
</style>
