<template>
  <div class="app-external-markdown">
    <vue-markdown class="c-markdown" :source="markdownText"></vue-markdown>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import axios, { AxiosResponse } from "axios";
import VueMarkdown from "vue-markdown";

@Component({
  components: {
    VueMarkdown
  }
})
export default class ExternalMarkdown extends Vue {
  @Prop({ default: "error.md" }) relativePath!: string;

  get markdownText() {
    try {
      if (
        require("raw-loader!@/datas/markdowns/" +
          this.$i18n.locale +
          "/" +
          this.relativePath)
      ) {
        return require("raw-loader!@/datas/markdowns/" +
          this.$i18n.locale +
          "/" +
          this.relativePath).default;
      }
    } catch (errorMessage) {
      console.warn(
        `"${
          this.relativePath
        }" does not exist in "${this.$store.getters.languageDictionary.get(
          this.$i18n.locale
        )}" language yet. It is loaded in fallback language, ${this.$store.getters.languageDictionary.get(
          this.$i18n.fallbackLocale
        )} instead.`
      );
    }
    return require("raw-loader!@/datas/markdowns/" +
      this.$i18n.fallbackLocale +
      "/" +
      this.relativePath).default;
  }

  mounted() {
    const externalMarkdowns: HTMLCollectionOf<
      Element
    > = document.getElementsByClassName("app-external-markdown");
    for (let externalMarkdown of externalMarkdowns) {
      if (externalMarkdown) {
        const links: NodeListOf<
          HTMLAnchorElement
        > = externalMarkdown.querySelectorAll("a");
        for (let link of links) {
          link.setAttribute("target", "_blank");
          link.setAttribute("rel", "noreferrer");
        }
      }
    }
  }
}
</script>