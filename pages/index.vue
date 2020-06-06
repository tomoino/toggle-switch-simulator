<template>
  <div class="container">
    <div>
      <h1 class="title">
        Toggle Switch Simulator
      </h1>
      <Equation />
      <vs-row>
        <vs-col vs-type="flex" vs-justify="center" vs-align="center" vs-lg="6" vs-sm="6" vs-xs="12">
          <Graph :values="firstGraphData"/>
        </vs-col>
        <vs-col vs-type="flex" vs-justify="center" vs-align="center" vs-lg="6" vs-sm="6" vs-xs="12">
          <Graph :values="secondGraphData"/>
        </vs-col> 
      </vs-row>
      <vs-row>
        <vs-col vs-type="flex" vs-justify="center" vs-align="center" vs-lg="6" vs-sm="6" vs-xs="12">
          <div style="margin:auto 50px;text-align: center;">
            <h3>U0 = {{Math.floor(u0*100)/100}}</h3><vs-slider v-model="u0Slider"/>
            <h3>V0 = {{Math.floor(v0*100)/100}}</h3><vs-slider v-model="v0Slider"/>
            <h3>I1 = {{Math.floor(I1*100)/100}}</h3><vs-slider :max="200" v-model="I1Slider"/>
            <h3>I2 = {{Math.floor(I2*100)/100}}</h3><vs-slider :max="200" v-model="I2Slider"/>
            <h3>α1 = {{Math.floor(a*100)/100}}</h3><vs-slider :max="200" v-model="aSlider"/>
            <h3>α2 = {{Math.floor(b*100)/100}}</h3><vs-slider :max="200" v-model="bSlider"/>
          </div>
        </vs-col>
        <vs-col vs-type="flex" vs-justify="center" vs-align="center" vs-lg="6" vs-sm="6" vs-xs="12">
          <Schale :value="stableU/3"/>    
        </vs-col> 
      </vs-row>

      
    </div>
  </div>
</template>

<script>
import Equation from '~/components/Equation.vue'
import Graph from '~/components/Graph.vue'
import Schale from '~/components/Schale.vue'

export default {
  components: {
    Equation,
    Graph,
    Schale
  },
  data () {
    return {
      u0Slider: 0,
      v0Slider: 0,
      I1Slider: 0,
      I2Slider: 0,
      aSlider: 100,
      bSlider: 100,
      stableU: 0,
    }
  },
  methods: {
    calcConcentration ( u0, v0, I1, I2, a, b) {
      const [t0, tmax, division] = [0, 30, 100]
      let U = [u0], V = [v0], T = [t0]
      let [u, v, t] = [u0, v0, t0]

      const n = 8
      const m = 8

      let dudt = (_u, _v) =>  (a / (1 + Math.pow(_v, n))) - _u + I1
      let dvdt = (_u, _v) =>  (b / (1 + Math.pow(_u, m))) - _v + I2
      const dt = (tmax-t0) / division
      
      for ( let i = 0; i <= division; i++) {
        u += dt * dudt(u, v)
        this.stableU = u
        v += dt * dvdt(u, v)
        t += dt
        U.push(u)
        V.push(v)
        T.push(Math.round(t*100)/100)
      }


      return {
        x:T,
        y:[
          {
            label: 'u',
            borderColor: 'rgba(255, 100, 130, 0.5)',
            fill: false,
            pointRadius: 0,
            data: U
          },
          {
            label: 'v',
            borderColor: 'rgba(100, 130, 255, 0.5)',
            fill: false,
            pointRadius: 0,
            data: V
          }
        ],
        options: {
          scales: {
            xAxes: [
              {
                scaleLabel: {
                  display: true, 
                  labelString: "時間 t"
                },
                ticks: {
                  beginAtZero: true,
                  min: 0,
                  max: 30,
                  maxTicksLimit:10
                }
              }
            ],
            yAxes: [
              {   
                scaleLabel: {
                  display: true, 
                  labelString: "転写因子濃度 u, v"
                },
                ticks: {
                  beginAtZero: true,
                  min: 0,
                }
              }
            ]
          },
          title: {
              display: true,
              position: "bottom",
              text: '転写因子濃度の時間変化'
          }
        }
      }
    },
    calcNullcline ( I1, I2, a, b) {
      let U = [], V_of_nullU = [], V_of_nullV = []

      const n = 8
      const m = 8

      let nullUMax = I1 + a
      let nullVMax = I2 + b

      let calc_u_of_nullU = (_v) =>  (a / (1 + Math.pow(_v, n))) + I1
      let calc_v_of_nullU = (_u) =>  _u - I1 == 0 ? 100 : (_u >= nullUMax ? -1 : Math.pow(a/(_u - I1) - 1, 1/n))
      let calc_v_of_nullV = (_u) =>  (b / (1 + Math.pow(_u, m))) + I2
      const du = 0.01

      for (let u = 0; u <= 4; u += du) {
        let v = calc_v_of_nullV(u)
        let v_of_nullU = calc_v_of_nullU(u)
        V_of_nullV.push(v)
        V_of_nullU.push(v_of_nullU)
        U.push(Math.round(u*100)/100)
      }

      return {
        x:U,
        y:[
          {
            label: 'du/dt=0',
            borderColor: 'rgba(255, 100, 130, 0.5)',
            fill: false,
            pointRadius: 0,
            data: V_of_nullU
          },
          {
            label: 'dv/dt=0',
            borderColor: 'rgba(100, 130, 255, 0.5)',
            fill: false,
            pointRadius: 0,
            data: V_of_nullV
          }
        ],
        options: {
          scales: {
            xAxes: [
              {
                scaleLabel: {
                  display: true, 
                  labelString: "転写因子濃度 u"
                },
                ticks: {
                  beginAtZero: true,
                  min: 0,
                  max: 4,
                  maxTicksLimit:10
                }
              }
            ],
            yAxes: [
              {   
                scaleLabel: {
                  display: true, 
                  labelString: "転写因子濃度 v"
                },
                ticks: {
                  beginAtZero: true,
                  min: 0,
                  max: 4
                }
              }
            ]
          },
          title: {
              display: true,
              position: "bottom",
              text: '転写因子濃度のヌルクライン'
          }
        }
      }
    }
  },
  computed: {
    firstGraphData: function() {
      return this.calcConcentration(this.u0,this.v0,this.I1,this.I2,this.a,this.b)
    },
    secondGraphData: function() {
      return this.calcNullcline(this.I1,this.I2,this.a,this.b)
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
    a: function(){
      return this.aSlider*0.01
    },
    b: function(){
      return this.bSlider*0.01
    },
  }
}
</script>

<style>
.container {
  margin: 0 auto;
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
  font-size: 40px;
  color: #35495e;
  letter-spacing: 1px;
  text-transform: capitalize;
  margin: 25px 0px
}

</style>
