<script setup lang="ts">
import { computed } from 'vue';
import CustomEditor from './components/CustomEditor.vue'
import DefaultEditorSnow from './components/DefaultEditorSnow.vue'
import DefaultEditorBubble from './components/DefaultEditorBubble.vue'
import VueMarkdown from 'vue-markdown-render'
import { useUrlSearchParams } from '@vueuse/core'
import ImageResizeEditor from './components/ImageResizeEditor.vue'
import SemanticHTMLEditor from './components/SemanticHTMLEditor.vue'
import { Analytics } from '@vercel/analytics/vue'

const params = useUrlSearchParams('hash-params')

const isSemanticTab = computed(() => params.tab === 'semantic')
const isCustomTab = computed(() => params.tab === 'custom')
const isDefaultSnowTab = computed(() => !params.tab || params.tab === 'snow')
const isImageResizeTab = computed(() => params.tab === 'image-resize')
const isDefaultBubbleTab = computed(() => params.tab === 'bubble')

const mdSrc = `
[![npm version](https://img.shields.io/npm/v/vue-quilly?logo=npm&logoColor=fff)](https://www.npmjs.com/package/vue-quilly)
[![npm downloads](https://img.shields.io/npm/dm/vue-quilly?color=green)](https://npm.chart.dev/vue-quilly)
[![NPM Type Definitions](https://img.shields.io/npm/types/vue-quilly)](https://www.npmjs.com/package/vue-quilly?activeTab=code)
[![GitHub License](https://img.shields.io/github/license/alekswebnet/vue-quilly)](https://github.com/alekswebnet/vue-quilly?tab=readme-ov-file#license)

- [Full Documentation](https://vue-quilly-docs.vercel.app/) - Comprehensive guide and API reference
- [Demo Source Code](https://github.com/vue-quilly/website) - Complete examples with different configurations
- [Nuxt 4 Integration](https://github.com/alekswebnet/vue-quilly/tree/main/nuxt) - SSR setup example
- [Browser CDN Setup](https://codepen.io/redrobot753/pen/VwJwPLP) - CodePen example
`

const menuItems = computed(() => [
  {
    title: 'Snow theme',
    tabKey: 'snow',
    selected: isDefaultSnowTab.value
  },
  {
    title: 'Bubble theme',
    tabKey: 'bubble',
    selected: isDefaultBubbleTab.value
  },
  {
    title: 'Semantic HTML',
    tabKey: 'semantic',
    selected: isSemanticTab.value
  },
  {
    title: 'Image Resize',
    tabKey: 'image-resize',
    selected: isImageResizeTab.value
  },
  {
    title: 'Custom Toolbar',
    tabKey: 'custom',
    selected: isCustomTab.value
  }
])

// Header links, icons from Simple Icons (CC0)
const headerLinks = [
  {
    title: 'GitHub',
    href: 'https://github.com/alekswebnet/vue-quilly',
    color: '#181717',
    icon: 'M12 .297c-6.63 0-12 5.373-12 12 0 5.303 3.438 9.8 8.205 11.385.6.113.82-.258.82-.577 0-.285-.01-1.04-.015-2.04-3.338.724-4.042-1.61-4.042-1.61C4.422 18.07 3.633 17.7 3.633 17.7c-1.087-.744.084-.729.084-.729 1.205.084 1.838 1.236 1.838 1.236 1.07 1.835 2.809 1.305 3.495.998.108-.776.417-1.305.76-1.605-2.665-.3-5.466-1.332-5.466-5.93 0-1.31.465-2.38 1.235-3.22-.135-.303-.54-1.523.105-3.176 0 0 1.005-.322 3.3 1.23.96-.267 1.98-.399 3-.405 1.02.006 2.04.138 3 .405 2.28-1.552 3.285-1.23 3.285-1.23.645 1.653.24 2.873.12 3.176.765.84 1.23 1.91 1.23 3.22 0 4.61-2.805 5.625-5.475 5.92.42.36.81 1.096.81 2.22 0 1.606-.015 2.896-.015 3.286 0 .315.21.69.825.57C20.565 22.092 24 17.592 24 12.297c0-6.627-5.373-12-12-12'
  },
  {
    title: 'npm',
    href: 'https://www.npmjs.com/package/vue-quilly',
    color: '#cb3837',
    icon: 'M1.763 0C.786 0 0 .786 0 1.763v20.474C0 23.214.786 24 1.763 24h20.474c.977 0 1.763-.786 1.763-1.763V1.763C24 .786 23.214 0 22.237 0zM5.13 5.323l13.837.019-.009 13.836h-3.464l.01-10.382h-3.456L12.04 19.17H5.113z'
  },
  {
    title: 'Support on Open Collective',
    href: 'https://opencollective.com/vuequilly',
    color: '#7fadf2',
    icon: 'M12 0C5.373 0 0 5.373 0 12s5.373 12 12 12c2.54 0 4.894-.79 6.834-2.135l-3.107-3.109a7.715 7.715 0 1 1 0-13.512l3.107-3.109A11.943 11.943 0 0 0 12 0zm9.865 5.166l-3.109 3.107A7.67 7.67 0 0 1 19.715 12a7.682 7.682 0 0 1-.959 3.727l3.109 3.107A11.943 11.943 0 0 0 24 12c0-2.54-.79-4.894-2.135-6.834z'
  }
]
</script>

<template>
  <div class="container" style="padding-top: 1rem; padding-bottom: 10rem;">
    <nav class="nav">
      <div class="nav-left">
        <h1><img width="40" height="40" src="/vue_quilly_logo.webp" alt="Vue Quilly Logo"> vue-quilly</h1>
      </div>
      <div class="nav-right">
        <a
          v-for="link in headerLinks"
          :key="link.href"
          :href="link.href"
          :title="link.title"
          :aria-label="link.title"
          target="_blank"
          class="button icon-only clear"
        >
          <svg width="24" height="24" viewBox="0 0 24 24" :fill="link.color" aria-hidden="true">
            <path :d="link.icon" />
          </svg>
        </a>
      </div>
    </nav>
    <p>Tiny Vue component, that helps to create Quill v2 based WYSIWYG editors</p>

    <vue-markdown class="markdown-content" :source="mdSrc" />

    <nav class="tabs" style="flex-wrap: wrap; margin-bottom: 3rem; row-gap: 1rem;">
        <a 
          v-for="item in menuItems" 
          :href="`#tab=${item.tabKey}`" 
          :class="{'active text-primary': item.selected }">
          {{ item.title }}
        </a>
    </nav>

    <DefaultEditorSnow v-if="isDefaultSnowTab" />
    <DefaultEditorBubble v-if="isDefaultBubbleTab" />
    <SemanticHTMLEditor v-if="isSemanticTab" />
    <ImageResizeEditor v-if="isImageResizeTab" />
    <CustomEditor v-if="isCustomTab" />
  </div>
  <Analytics />
</template>

<style>
body, html {
  margin: 0;
  padding: 0;
  word-break: break-all;
}
:root {
  --bg-color: #ffffff;
  --bg-secondary-color: #2d2d2d;
  --color-primary: #3ca877;
  --color-lightGrey: #d2d6dd;
  --color-grey: #747681;
  --color-darkGrey: #3f4144;
  --color-error: #d43939;
  --color-success: #28bd14;
  --grid-maxWidth: 120rem;
  --grid-gutter: 2rem;
  --font-size: 16px;
  --font-color: #333333;
  --font-family-sans: sans-serif;
  --font-family-mono: monaco, "Consolas", "Lucida Console", monospace;
}
.markdown-content {
  margin-bottom: 5rem;
}
</style>