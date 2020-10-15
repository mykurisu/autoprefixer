<template>
  <div id="codemirror">
    <codemirror
      :value="this.formatCss"
      :options="cmOptions"
      @ready="onCmReady"
      @input="onCmCodeChange"
    />
  </div>
</template>

<script>
import { codemirror } from "vue-codemirror";
import prettier from "prettier/standalone";
import parserCss from "prettier/parser-postcss";
import "codemirror/mode/css/css";
import "codemirror/mode/sass/sass";
import "codemirror/lib/codemirror.css";
import "codemirror/theme/monokai.css";

export default {
  components: {
    codemirror,
  },
  props: ["width", "height", "value", "readOnly"],
  computed: {
    formatCss: function () {
      const f = prettier.format(this.value || '', {
        parser: 'css',
        plugins: [parserCss]
      });
      
      return f || this.value
    }
  },
  data() {
    return {
      code: "",
      cmOptions: {
        tabSize: 4,
        mode: "markdown",
        theme: "monokai",
        lineNumbers: true,
        line: true,
        lineWrapping: true,
      },
    };
  },
  methods: {
    onCmReady(cm) {
      const { width = "100%", height = "300px" } = this;
      cm.setSize(width, height);
    },
    onCmCodeChange(newCode) {
      this.code = newCode;
      this.$emit("change", { value: newCode });
    },
    onCmFocus() {
      this.$emit("focus");
    },
  },
};
</script>
