<template>
  <div class="container">
    <div>
      <h1 class="title">
        Toggle Switch Simulator
      </h1>
      <Graph :values="firstGraphData"/>
      <Graph :values="secondGraphData"/>
      <div>
        <h3>U0</h3><vs-slider v-model="u0"/>
        <h3>V0</h3><vs-slider v-model="v0"/>
        <h3>I1</h3><vs-slider v-model="I1"/>
        <h3>I2</h3><vs-slider v-model="I2"/>
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
      u0: 0,
      v0: 0,
      I1: 1,
      I2: 1,
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
        T.push(t)
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
      const [nullMax, division] = [2.0, 200]
      let U = [], V_of_nullU = [], V_of_nullV = []

      const [a, n] = [1, 8]
      const [b, m] = [1, 8]

      const nullUMax = I1 + a 
      const nullVMax = I2 + b

      let calc_u_of_nullU = (_v) =>  (a / (1 + Math.pow(_v, n))) + I1
      let calc_v_of_nullU = (_u) =>  Math.pow(a/(_u - I1) - 1, 1/n)
      let calc_v_of_nullV = (_u) =>  (b / (1 + Math.pow(_u, m))) + I2
      const du = nullMax / division
      const dv = nullMax / division

      for (let u = 0; u < nullMax; u += du) {
        let v = calc_v_of_nullV(u)
        let v_of_nullU = calc_v_of_nullU(u)
        console.log(u+": v = "+v_of_nullU)
        V_of_nullV.push(v)
        V_of_nullU.push(v_of_nullU)
        U.push(u)
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
