# シミュレータに使用した技術まとめ

# 数式の書き方
## 導入
```bash
yarn add @nuxtjs/markdownit
yarn add markdown-it-katex
``` 
nuxt.config.js
```JS
export default {
  ///
  modules: [
    '@nuxtjs/markdownit'
  ],
  markdownit: {
    preset: "default",
    html: true,
    linkify: true,
    breaks: true,
    typographer: true,
    injected: true,
    use: [
      "markdown-it-katex",
    ]
  },
  ///
}
```

## 使い方
$md.render()内に指定した文章がHTML形式に変換される。$で囲んだ部分が数式に変換される。
なぜだか、\ではうまくいかず、\\にしたらうまくいった。（原因不明）
```JS
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

```

# グラフの書き方

# 参考
[【Nuxt】@nuxtjs/markdownitでマークダウンからhtmlに生成する方法](https://gurutaka-log.com/nuxtjs-markdownit-howto)
[Nuxt.js製サイトにKaTeX(markdown-it-katex)で数式表示](https://blog.9sako6.site/posts/nuxt_markdown_it_katex)