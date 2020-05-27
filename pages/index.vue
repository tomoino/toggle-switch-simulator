<template>
  <div class="container">
    <div>
      <header class="content-logos">
        <logo />
        <span class="plus">+</span>
        <VuesaxLogo />
      </header>
      <h1 class="title">
        Toggle Switch Simulator
      </h1>
      <Graph/>
    </div>
  </div>
</template>

<script>
import Graph from '~/components/Graph.vue'

export default {
  components: {
    Graph
  },
  methods: {
    calcConcentration ( u0, v0, I1, I2) {
      const [t0, tmax, division] = [0, 30, 100]
      let U = [], V = [], T = []
      let [u, v, t] = [u0, v0, t0]

      const [a, n, I1] = [1, 8, 1]
      const [b, m, I2] = [1, 8, 1]

      let dudt = (_u, _v) =>  (a / (1 + Math.pow(_v, n))) - _u + I1
      let dvdt = (_u, _v) =>  (b / (1 + Math.pow(_u, m))) - _v + I2
      const dt = (tmax-t0) / division
      
      for ( let i = 0; i < division; i++) {
        u += dt * dudt(u, v)
        v += dt * dvdt(u, v)
        t += dt
        U.push(u)
        V.push(v)
        T.push(t)
      }

      return U,V,T; 
    }
  },
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
