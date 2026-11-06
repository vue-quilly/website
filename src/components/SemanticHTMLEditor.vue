<script setup lang="ts">
import { ref, shallowRef } from 'vue'
import { QuillyEditor } from 'vue-quilly'
import type Quill from 'quill'
import type { Delta, Range } from 'quill'
import 'quill/dist/quill.snow.css'

const model = ref<string>(`<h1>Hello world!</h1><p><strong>bold</strong></p><ol><li>item 1</li><li>item 2</li><li>item 3</li></ol><p></p><ul><li>item 4</li><li>item 5</li><li>item 6</li></ul><p>I am a new paragraph</p>`)
const quill = shallowRef<Quill>()
const editorDelta = ref<Delta>()
const editorRange = ref<Range>()

const options = ref({
  theme: 'snow',
  modules: {
    toolbar: [
      [{ font: [] }, { size: [] }],
      ['bold', 'italic', 'underline', 'strike'],
      [{ color: [] }, { background: [] }],
      [{ script: 'super' }, { script: 'sub' }],
      [{ header: '1' }, { header: '2' }, 'blockquote', 'code-block'],
      [{ list: 'ordered' }, { list: 'bullet' }, { indent: '-1' }, { indent: '+1' }],
      ['direction', { align: [] }],
      ['link', 'image', 'video'],
      ['clean']
    ]
  },
  placeholder: 'Insert text here ...',
  readOnly: false
})

// Quill loads after mount, in its own chunk
const loadQuill = () => import('quill')

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
  <QuillyEditor
    v-model="model"
    :options="options"
    :quill-class="loadQuill"
    @ready="quill = $event"
    :is-semantic-html-model="true"
    @update:model-value="onModelValueChange"
    @text-change="onTextChange"
    @selection-change="onSelectionChange"
    @editor-change="onEditorChange"
  />
  <hr>
  <p class="tag">MODEL:</p>
  <p><small>{{ model }}</small></p>
  <button class="button" @click="model = `<h1>Hello world!</h1><p>I am a new paragraph</p>`">Set model</button>
  <button class="button" @click="quill?.setContents([])">Reset model</button>
  <hr>
  <p class="tag">CONTENTS:</p>
  <p><small>{{ quill?.getContents() }}</small></p>
  <button
    class="button"
    @click="setSampleContents"
  >
    Set contents
  </button>
  <button class="button" @click="quill?.setContents([])">Reset contents</button>
  <hr>
  <p class="tag">LAST CHANGE:</p>
  <p><small>{{ editorDelta }}</small></p>
  <hr>
  <p class="tag">CURRENT SELECTION:</p>
  <p><small>{{ editorRange }}</small></p>
  <button class="button" @click="quill?.setSelection({ index: 5, length: 2 })">Set range</button>
  <hr>
  <p class="tag">CONTENT LENGTH:</p>
  <p><small>{{ quill?.getLength() }}</small></p>
  <hr>
  <p class="tag">Readonly:</p>
  <p><input :value="quill?.isEnabled()" type="checkbox" @input="quill?.enable(!quill?.isEnabled())"/></p>
</template>
