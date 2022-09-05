<script setup>
  import { ref, computed } from "vue";
  import { marked } from "marked";
  import prism from "prismjs";
  
  // Add numbering to the Code blocks
  import "prismjs/plugins/line-numbers/prism-line-numbers.js";
  import "prismjs/plugins/line-numbers/prism-line-numbers.css";
  
  // This is needed if you have a conflict with other loaded CSS imports (i.e. Bulma).
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
  