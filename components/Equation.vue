<template>
    <div style="margin-bottom: 50px;">
        <h5 class="headline">リプレッサー濃度u, vの変化速度</h5>
        <p v-html="$md.render('$\\frac{du}{dt} = \\frac{\\alpha_1}{1+v^{\\beta}} - u + I_1$')"></p>
        <p v-html="$md.render('$\\frac{dv}{dt} = \\frac{\\alpha_2}{1+u^{\\gamma}} - v + I_2$')"></p>
    </div>
</template>
 
<script>
import MarkdownIt from 'markdown-it'
import markdownItKatex from 'markdown-it-katex'
import "~/node_modules/katex/dist/katex.min.css"

export default {
}
</script>

<style>
.headline {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 100;
  font-size: 3vh;
  color: #35495e;
  letter-spacing: 1px;
  margin: 25px 0px
}

</style>
