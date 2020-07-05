<template>
    <div style="margin-bottom: 50px;">
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
