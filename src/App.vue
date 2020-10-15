<template>
  <div id="app">
    <div class="code-area">
      <button class="format" @click="handleFormat">格式化</button>
      <codemirror
        :value="codeValue"
        :options="cmOptions"
        :class="previewShow ? 'code-normal' : 'code-full'"
        @input="handleChange"
        @ready="onCmReady"
      />
    </div>
    <div class="code-area">
      <codemirror
        :value="prefixValue"
        :options="prOptions"
        :class="previewShow ? 'code-normal' : 'code-full'"
        @ready="onCmReady"
      />
    </div>
  </div>
</template>

<script>
import { codemirror } from "vue-codemirror";
import autoprefixer from "autoprefixer";
import postcss from "postcss";
import prettier from "prettier/standalone";
import parserCss from "prettier/parser-postcss";
import "codemirror/mode/css/css";
import "codemirror/mode/sass/sass";
import "codemirror/lib/codemirror.css";
import "codemirror/theme/monokai.css";

export default {
  name: "App",
  components: {
    codemirror,
  },
  data() {
    return {
      inited: false,
      previewShow: false,
      codeValue: "",
      prefixValue: "",
      cmOptions: {
        tabSize: 4,
        mode: "css",
        lineNumbers: true,
        line: true,
        lineWrapping: true,
      },
      prOptions: {
        tabSize: 4,
        mode: "css",
        theme: "monokai",
        readOnly: true,
        lineNumbers: true,
        line: true,
        lineWrapping: true,
      }
    };
  },
  methods: {
    onCmReady(cm) {
      const { width = "100%", height = "100vh" } = this;
      cm.setSize(width, height);
    },
    handleChange(value) {
      this.codeValue = value;

      this.handlePrefix(value).then((res) => {
        this.prefixValue = res;
      });
    },
    handleFormat() {
      const pv = prettier.format(this.codeValue || '', {
        parser: 'css',
        plugins: [parserCss]
      }) || this.codeValue;
      this.handleChange(pv)
    },
    handlePrefix(css) {
      return new Promise((resolve) => {
        postcss([autoprefixer({ overrideBrowserslist: ["iOS 7"] })])
          .process(css)
          .then((result) => {
            resolve(result.css || css);
          });
      });
    },
  },
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  border: 0;
  height: 100%;
}
#app {
  height: 100%;
  display: flex;
  justify-content: space-between;
}
.code-area {
  width: 50%;
  position: relative;
}
.format {
  position: absolute;
  width: 60px;
  height: 20px;
  border-radius: 5px;
  text-align: center;
  top: 10px;
  right: 10px;
  z-index: 10;
}
</style>
