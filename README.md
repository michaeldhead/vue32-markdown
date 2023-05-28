# Using Markdown Highlighting in Vue

## What is Markdown?
Markdown is a **markup language** that is designed to be easy for non-technical people to read and write. It is used to format text in a way that is both simple and easy-to-use. Markdown was created with the goal of being appealing to human readers in its source code form. It is widely used in blogging, online forums, documentation pages, and readme files. In the easiest terms, Markdown is used to add formatting elements to plain text documents. Anyone can use Markdown to create a document, even those with no prior experience with markup languages.

## How do you create a Markdown document?
First, you will need to create a Markdown file using a text editor or a dedicated Markdown application with an `.md` or `.markdown` extension. Then, you can use a Markdown application to convert the Markdown file to an HTML document. There are many resources available online to help you get started with Markdown, including tutorials and reference guides. I mainly use [Ghostwriter](https://ghostwriter.kde.org/) for my Markdown writting.

To create a simple document using Markdown, you can follow these steps:

1. Open a text editor or a dedicated Markdown application.
2. Start writing your document using plain text.
3. Add Markdown syntax to the text to indicate which words and phrases should look different. For example, to denote a heading, you add a number sign before it (e.g., `# Heading One`). Or to make a phrase bold, you add two asterisks before and after it (e.g., `**this text is bold**`).
4. Save your file with an `.md` or `.markdown` extension.

Markdown provides a wide range of formatting options to help you structure and style your text. These are just a few examples of the formatting options available in Markdown. There are many more options available, including creating tables, adding code blocks, and using blockquotes.

## Using the Markdown document
We will need to use a Markdown application to convert the Markdown file to an HTML document. We will be using [marked.js](https://marked.js.org/) for the conversion to HTML and displaying it in our Vue application.

## Syntax Highlighting
The biggest issue in displaying the Markdown is when you want to show a Code Block or Code Snippet. Code Block highlighting is done by a syntax highlighter. There are several articles about integrating marked.js into Vue. But the highlighting of the Code Block is a different story. This is where we will use another tool for the Highlighting. For this, we will be using [Prismjs](https://prismjs.com/).

## How can we do all of this in Vue?
Hopefully we can solve that issue with by using `marked.js` and `prismjs`. Let's start by taking a look at the basic parts needed to view a Markdown document in a Vue 3 application.

### To install the required items:
```js
npm install marked
npm install prismjs
```

## Minimum Items Needed in the Vue file
We will start with the minimum items need to read in the Markdown text and display it with the proper Code Block highlighter. So let's get started.

We need to import the engine that will read the Markdown text and convert it to HTML
```js
// Takes the markdown file and creates an HTML output
import { marked } from "marked"; 
```
We now need to import the Syntax Highligher for the Code Blocks
```js
// Needed to highlight the Code Block created in the markdown
import prism from "prismjs";
```
We need to tell `prismjs` what Language to use to highlight. i.e. `javascript` or `js`, `csharp` or `cs`, `json`.
```js
// Marked options. See https://marked.js.org/using_advanced#options for more information
return prism.highlight(code, prism.languages[lang], lang);
```
Apply the Syntax 
```js
// apply the highlight syntax to the Code Block
prism.highlightAll();
```
Use the Markdown text in the HTML page
```js
// use the markdown in the Vue template
marked.parse(markDown.value);
```
Use a predefined CSS style to the Code Block
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

### Add Copy and Show Language
```js
import "prismjs/plugins/toolbar/prism-toolbar.js"; // required for the following plugins
import "prismjs/plugins/toolbar/prism-toolbar.css"; // required for the following plugins
import "prismjs/plugins/copy-to-clipboard/prism-copy-to-clipboard.js"; // show copy button
import "prismjs/plugins/show-language/prism-show-language.js"; // display the language of the code block

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
Now let's put it all together using Vue 3.

`App.vue`
```js
<script setup>
import { ref, computed } from "vue";
import { marked } from "marked";
import prism from "prismjs";

// Add numbering to the Code blocks
import "prismjs/plugins/line-numbers/prism-line-numbers.js";
import "prismjs/plugins/line-numbers/prism-line-numbers.css";

import "prismjs/plugins/toolbar/prism-toolbar.js"; // required for the following plugins
import "prismjs/plugins/toolbar/prism-toolbar.css"; // required for the following plugins
import "prismjs/plugins/copy-to-clipboard/prism-copy-to-clipboard.js"; // show copy button
import "prismjs/plugins/show-language/prism-show-language.js"; // display the language of the code block

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

### More information can be found here:
- [marked.js](https://marked.js.org/)
- [Prismjs](https://prismjs.com/)
- [Ghostwriter](https://ghostwriter.kde.org/)

## Live Demo
[Live demo](https://blog.theheadfamily.com/blog/blog3)
