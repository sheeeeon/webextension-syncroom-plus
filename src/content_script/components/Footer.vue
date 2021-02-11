<template lang="pug">
footer.footer
  .content.has-text-centered
    p
      b-tag(type="is-info")
        | SYNCROOM Plus Version {{ version }} + 한국어 번역

    p
      strong SYNCROOM Plus
      |
      | 확장 프로그램은
      |
      a(href='https://twitter.com/koedamedev', target="_blank", rel="noopener noreferrer")
        strong
          | 肥溜め
        | @koedamedev
      |
      | 님이 관리・운용하고 있습니다.

    p
      strong 한국어 번역 
      |
      | 은
      |
      a(href='', target="_blank", rel="noopener noreferrer")
        strong
          | shion
        | 
      |
      | 이 하였으며 비공식 배포판입니다.
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api';
const browser = require('webextension-polyfill');

export default defineComponent({
  setup() {
    return {
      version: browser.runtime.getManifest().version,
    };
  },
});
</script>
