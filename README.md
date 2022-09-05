# Markdown with Code Block Highlighting in Vue

There are quite a few articles about markdown files and how useful they can be for a website. For example, the "Release Notes" will be written by someone who does not know HTML but needs to be on the website. Markdown language can do that because markdown allows someone to create a document that can get converted to HTML seamlessly.

The issue now comes down to "How can we do that in Vue?" The answer is `marked.js` and `prismjs`

The biggest issue is the Code Block highlighting. There are several articles about integrating `marked.js` into Vue. But the highlighting of the Code Block is a different story. Hopefully we can solve that issue.

### More information can be found here:
- [marked.js](https://marked.js.org/)
- [Prismjs](https://prismjs.com/)

## Live Demo
[Live demo](https://blog.theheadfamily.com/blog/blog3)

### To install the required items:
```js
npm install marked
npm install prismjs
```

## Minimum Items Needed
```js
// Takes the markdown file and creates an HTML output
import { marked } from "marked"; 
```
```js
// To highlight the Code Block created in the markdown
import prism from "prismjs";
```
```js
// Marked options. See https://marked.js.org/using_advanced#options for more information
return prism.highlight(code, prism.languages[lang], lang);
```
```js
// apply the highlight syntax to the Code Block
prism.highlightAll();
```
```js
// use the markdown in the Vue template
marked.parse(markDown.value);
```
```js
// a style to apply to the Code Block
@import "prismjs/themes/prism-coy.css"; 
```

## Advanced Options
### Use Custom Class Mapping
```js
// This is needed if you have a conflict with other loaded CSS imports (i.e. Bulma).
import "prismjs/plugins/custom-class/prism-custom-class";
prism.plugins.customClass.map({ number: "prism-number", tag: "prism-tag" });
```

### Add Numbering
```js
// Add numbering to the Code blocks
import "prismjs/plugins/line-numbers/prism-line-numbers.js";
import "prismjs/plugins/line-numbers/prism-line-numbers.css";

<!-- Normal Code Blocks -->
	<div v-html="mdToHtml"></div>
<!-- Change the above to Add numbering to the Code Blocks -->
	<div class="line-numbers language-markup" v-html="mdToHtml"></div>
```
See the [prismjs.com plugins](https://prismjs.com/index.html#plugins) here.



### Change marked Options
```js
marked.use({
  gfm: true,
  highlight: (code, lang) => {
    if (prism.languages[lang]) {
      return prism.highlight(code, prism.languages[lang], lang);
    } else {
      return code;
    }
  },
  silent: true,
  smartypants: true,
});
```
See [marked Options](https://marked.js.org/using_advanced#options) for more information

### Change the Code Block Theme
```js
<style>
/* @import "prismjs/themes/prism-tomorrow.css"; */
/* @import "prismjs/themes/prism.css"; */
@import "prismjs/themes/prism-coy.css"; 
</style>
```
See [https://prismjs.com/](https://prismjs.com/) for more information

## The whole solution
`App.vue`
```js
<script setup>
import { ref, computed } from "vue";
import { marked } from "marked";
import prism from "prismjs";

// Add numbering to the Code blocks
import "prismjs/plugins/line-numbers/prism-line-numbers.js";
import "prismjs/plugins/line-numbers/prism-line-numbers.css";

// This is needed for a conflict with other CSS files being used (i.e. Bulma).
import "prismjs/plugins/custom-class/prism-custom-class";
prism.plugins.customClass.map({ number: "prism-number", tag: "prism-tag" });

// sample markdown file
const mdfile = "/sample.md";
const markDown = ref("");

//marked Options => https://marked.js.org/using_advanced#options
marked.use({
  highlight: (code, lang) => {
    if (prism.languages[lang]) {
      return prism.highlight(code, prism.languages[lang], lang);
    } else {
      return code;
    }
  },
});

// read in the md file and apply the highlight style to the Code Block
const getMarkdownData = async () => {
  await fetch(mdfile)
    .then((response) => response.text())
    .then((data) => (markDown.value = data));
  prism.highlightAll(); // perform the highlighting of the Code Blocks
};

// use the finialized markdown to HTML code in the template
const mdToHtml = computed(() => {
  const mdhtml = marked.parse(markDown.value);
  return mdhtml;
});

// call the function to be ran
getMarkdownData();
</script>

<template>
  <div class="content">
    <!-- Normal Code Blocks -->
    <!-- <div v-html="mdToHtml"></div> -->
    <!-- Add numbering to the Code Blocks -->
    <div class="line-numbers language-markup" v-html="mdToHtml"></div>
  </div>
</template>

<style>
/* Add the prism themes here */
@import "prismjs/themes/prism-coy.css";
</style>
```
