<script setup lang="ts">
import { ref, shallowRef } from 'vue'
import { QuillyEditor } from 'vue-quilly'
import type Quill from 'quill/core'
import type { Delta, Range } from 'quill/core'
import 'quill/dist/quill.core.css'

const model = ref<string>('<h1>Hello world!</h1><p>I am a new paragraph</p>')
const quill = shallowRef<Quill>()
const editorDelta = ref<Delta>()
const editorRange = ref<Range>()

const options = {
  placeholder: 'Start your story here...',
  // Keep these formats only, even after another tab has loaded the full Quill build
  formats: ['bold', 'italic', 'header']
}

// quill/core with bold, italic and header, loaded after mount
const loadQuill = () => import('../quill/core')

// Apply a format to the selection, or remove it if it's already applied
const toggle = (format: string, value: unknown = true) => {
  const editor = quill.value
  if (!editor) return
  editor.format(format, editor.getFormat()[format] === value ? false : value)
}

// Replace the content through the Quill API
const setSampleContents = () => {
  quill.value?.setContents([
    { insert: 'Hello' },
    { insert: '\n', attributes: { header: 1 } },
    { insert: 'Some ' },
    { insert: 'initial', attributes: { bold: true } },
    { insert: ' ' },
    { insert: 'content', attributes: { underline: true } },
    { insert: '\n' }
  ])
}

const onModelValueChange = (value: string) => console.log(value)
const onTextChange = (({ delta }: { delta: Delta }) => (editorDelta.value = delta))
const onSelectionChange = ({ range }: { range: Range }) => (editorRange.value = range)
const onEditorChange = (eventName: string) => console.log(eventName)
</script>

<template>
  <div>
    <button class="button" @click="toggle('bold')">Bold</button>
    <button class="button" @click="toggle('italic')">Italic</button>
    <button class="button" @click="toggle('header', 1)">Header 1</button>
    <button class="button" @click="toggle('header', 2)">Header 2</button>
  </div>

  <QuillyEditor
    v-model="model"
    :options="options"
    :quill-class="loadQuill"
    @ready="quill = $event"
    @update:model-value="onModelValueChange"
    @text-change="onTextChange"
    @selection-change="onSelectionChange"
    @editor-change="onEditorChange"
  />

  <hr>
  <p class="tag">Model value:</p>
  <p><small>{{ model }}</small></p>
  <button class="button" @click="model = `<h1>Hello world!</h1><p>I am a new paragraph</p>`">Set model value</button>
  <button class="button" @click="quill?.setContents([])">Reset model</button>
  <hr>
  <p class="tag">Contents:</p>
  <p><small>{{ quill?.getContents() }}</small></p>
  <button class="button" @click="setSampleContents">
    Set contents
  </button>
  <button class="button" @click="quill?.setContents([])">Reset contents</button>
  <hr>
  <p class="tag">SEMANTIC HTML:</p>
  <p><small>{{ quill?.getSemanticHTML() }}</small></p>
  <hr>
  <p class="tag">Last change:</p>
  <p><small>{{ editorDelta }}</small></p>
  <hr>
  <p class="tag">Current selection:</p>
  <p><small>{{ editorRange }}</small></p>
  <button class="button" @click="quill?.setSelection({ index: 5, length: 2 })">Set range</button>
  <hr>
  <p class="tag">Content length:</p>
  <p><small>{{ quill?.getLength() }}</small></p>
  <hr>
  <p class="tag">Readonly:</p>
  <p><input :value="quill?.isEnabled()" type="checkbox" @input="quill?.enable(!quill?.isEnabled())"/></p>
</template>
