<template>
  <div class="container" style="padding: 20px 30vw;">
    <div>
      <h1 class="title">
        Toggle Switch Simulator
      </h1>
      <div>
        <img src="~/assets/fig1.png" width="100%"/>
        <br>
        <img src="~/assets/fig2.png" width="100%"/>
      </div> 
      <Equation style="font-size: 4vh;"/>
      <vs-row>
        <vs-col vs-type="flex" vs-justify="center" vs-align="flex-start" vs-lg="6" vs-sm="6" vs-xs="12">
          <Graph :values="firstGraphData" style="width:94%;"/>
        </vs-col>
        <vs-col vs-type="flex" vs-justify="center" vs-align="flex-start" vs-lg="6" vs-sm="6" vs-xs="12">
          <div>
            <Graph :values="secondGraphData" style="width:94%;" />
            <vs-switch v-model="nullclineDisplay" style="margin: auto;">
              <span slot="on">ヌルクライン表示</span>
              <span slot="off">ヌルクライン非表示</span>
            </vs-switch>
          </div>
        </vs-col> 
      </vs-row>
      <vs-row>
        <vs-col vs-type="flex" vs-justify="center" vs-align="center" vs-lg="6" vs-sm="6" vs-xs="12">
          <div style="margin:50px auto;text-align: center;">
            <h3 style="margin-bottom:-12px;">u0 = {{Math.floor(u0*100)/100}}</h3><vs-slider :max="U0_MAX*100" v-model="u0Slider"/>
            <h3 style="margin-bottom:-12px;margin-top:25px;">v0 = {{Math.floor(v0*100)/100}}</h3><vs-slider :max="V0_MAX*100"  v-model="v0Slider"/>
            <h3 style="margin-bottom:-12px;margin-top:25px;">I1 = {{Math.floor(I1*100)/100}}</h3><vs-slider :max="I1_MAX*100" v-model="I1Slider"/>
            <h3 style="margin-bottom:-12px;margin-top:25px;">I2 = {{Math.floor(I2*100)/100}}</h3><vs-slider :max="I2_MAX*100" v-model="I2Slider"/>
            <h3 style="margin-bottom:-12px;margin-top:25px;">α1 = {{Math.floor(a*100)/100}}</h3><vs-slider :max="ALPHA1_MAX*100" v-model="aSlider"/>
            <h3 style="margin-bottom:-12px;margin-top:25px;">α2 = {{Math.floor(b*100)/100}}</h3><vs-slider :max="ALPHA2_MAX*100" v-model="bSlider"/>
            <h3 style="margin-top:25px;">β = 8, γ = 8</h3>
          </div>
        </vs-col>
        <vs-col vs-type="flex" vs-justify="center" vs-align="center" vs-lg="6" vs-sm="6" vs-xs="12">
          <vs-row>
            <vs-col vs-type="flex" vs-justify="center" vs-align="center" vs-lg="12" vs-sm="12" vs-xs="12">
              <Schale :value="stableU/3"/>
            </vs-col>
            <vs-col style="margin-top: 30px;" vs-type="flex" vs-justify="center" vs-align="center" vs-lg="12" vs-sm="12" vs-xs="12">
              <p>※転写因子濃度uに依存してGFPが発現</p>
            </vs-col>
          </vs-row>
        </vs-col> 
      </vs-row>
    </div>
  </div>
</template>

<script>
import Equation from '~/components/Equation.vue'
import Graph from '~/components/Graph.vue'
import Schale from '~/components/Schale.vue'
import Figure from '~/components/Figure.vue'

export default {
  components: {
    Equation,
    Graph,
    Schale,
    Figure
  },
  data () {
    return {
      U0_MAX: 2,
      V0_MAX: 2,
      I1_MAX: 2,
      I2_MAX: 2,
      ALPHA1_MAX: 2,
      ALPHA2_MAX: 2,
      u0Slider: 0,
      v0Slider: 0,
      I1Slider: 0,
      I2Slider: 0,
      aSlider: 100,
      bSlider: 100,
      stableU: 0,
      uLast: 0,
      vLast: 0,
      nullclineDisplay: false
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

        if (i == division) {
          this.uLast = u
          this.vLast = v
        }
      }


      return {
        x:T,
        y:[
          {
            label: 'u',
            pointBackgroundColor: 'rgba(255, 100, 130,  0.5)',
            borderColor: 'rgba(255, 100, 130, 0.5)',
            fill: false,
            pointRadius: 0,
            data: U
          },
          {
            label: 'v',
            pointBackgroundColor: 'rgba(100, 130, 255,  0.5)',
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
          },
          legend: {
            labels: {
              usePointStyle: true
            }
          }
        }
      }
    },
    calcNullcline ( I1, I2, a, b) {
      let U = [], V_of_nullU = [], V_of_nullV = []

      const n = 8
      const m = 8
      const U_MAX = 3
      const V_MAX = 3

      let nullUMax = I1 + a
      let nullVMax = I2 + b

      let calc_u_of_nullU = (_v) =>  (a / (1 + Math.pow(_v, n))) + I1
      let calc_v_of_nullU = (_u) =>  _u - I1 == 0 ? 100 : (_u >= nullUMax ? -1 : Math.pow(a/(_u - I1) - 1, 1/n))
      let calc_v_of_nullV = (_u) =>  (b / (1 + Math.pow(_u, m))) + I2
      const du = 0.01

      for (let u = 0; u <= U_MAX; u += du) {
        let v = calc_v_of_nullV(u)
        let v_of_nullU = calc_v_of_nullU(u)
        V_of_nullV.push(v)
        V_of_nullU.push(v_of_nullU)
        U.push(Math.round(u*100)/100)
      }

      let init = Array(U.length).fill(NaN)
      let last = Array(U.length).fill(NaN)
      // 初期値の近似値と最終値の近似値のインデックス 
      let initApproIndex = 0
      let lastApproIndex = 0
      
      for (let i = 0; i < U.length; i++) {
        if (Math.abs(U[i] - this.u0) < Math.abs(U[initApproIndex] - this.u0)) {
          initApproIndex = i
        }

        if (Math.abs(U[i] - this.uLast) < Math.abs(U[lastApproIndex] - this.uLast)) {
          lastApproIndex = i
        }
      }

      init[initApproIndex] = this.v0
      last[lastApproIndex] = this.vLast

      return {
        x:U,
        y:[
          {
            label: 'du/dt=0',
            pointBackgroundColor: 'rgba(255, 100, 130,  0.5)',
            borderColor: 'rgba(255, 100, 130, 0.5)',
            fill: false,
            pointRadius: 0,
            data: V_of_nullU
          },
          {
            label: 'dv/dt=0',
            pointBackgroundColor: 'rgba(100, 130, 255,  0.5)',
            borderColor: 'rgba(100, 130, 255, 0.5)',
            fill: false,
            pointRadius: 0,
            data: V_of_nullV
          },
          {
            label: '初期濃度',
            pointBackgroundColor: 'rgba(255, 100, 100,  0.8)',
            borderColor: 'rgba(255, 100, 100,  0.8)',
            fill: false,
            pointRadius: 5,
            data: init
          },
          {
            label: '最終濃度',
            pointBackgroundColor: 'rgba(100, 100, 255,  0.8)',
            borderColor: 'rgba(100, 100, 255, 0.8)',
            fill: false,
            pointRadius: 5,
            data: last
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
                  max: U_MAX,
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
                  max: V_MAX
                }
              }
            ]
          },
          title: {
              display: true,
              position: "bottom",
              text: '転写因子濃度のヌルクライン'
          },
          legend: {
            labels: {
              usePointStyle: true
            }
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
  font-size: 5vh;
  color: #35495e;
  letter-spacing: 1px;
  text-transform: capitalize;
  margin: 25px 0px
}

</style>
