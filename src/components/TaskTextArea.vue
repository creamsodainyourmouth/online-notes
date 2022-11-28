// source https://pcvector.net/scripts/forms/762-textarea-auto-height.html

// another>>> https://codepen.io/iancwoodward/pen/gOLRBZy
// >>> https://codepen.io/TylerCarrington/pen/PooMjR
// >>> https://css-tricks.com/auto-growing-inputs-textareas/
<template>
  <div class="TaskTextArea">
    <textarea rows="1" @input="inputHandler"
              v-model="text"
              @keydown.enter.prevent="enterHandler"
              @focus="focusHandler"
              @blur="blurHandler"
              :placeholder="placeholder"
              maxlength="250"
              ref="textarea">
    </textarea>
  </div>
</template>


<script>
export default {
  name: "CustomTextArea",
  props: {
    content: {
      type: String,
      // required: true
    },
    editing: {
      type: Boolean,
    }
  },
  data() {
    return {
      scrollTest: null,
      text: this.content,
      initiated: false,
      placeholder: "New task..."
    }
  },

  mounted() {
    // No reactive
    // this.scrollTest = this.$refs.textarea.scrollHeight
    console.log('hello from custom textarea>>>')
    console.log('my content body rask>>>', this.content)
    this.resize()
  },

  methods: {
    inputHandler() {
      this.$emit('task-created')
      this.updateContent()
      this.resize()
      this.placeholder = false
    },

    updateContent() {
      this.$emit('content-updated', this.text)
    },

    focusHandler() {
      this.$emit('content-focused')
    },

    enterHandler() {
      this.$emit('edit-finished')
    },

    blurHandler() {
      this.$emit('content-blurred')
    },

    resize() {
      this.$refs.textarea.style.height = "auto"
      this.$refs.textarea.style.height =`${this.$refs.textarea.scrollHeight}px`
    },

    stopEditing() {
      this.editing = !this.editing
    },
  },

}
</script>

<style scoped>
  .TaskTextArea {
    width: 86%;
    display: flex;
  }
  textarea {
    resize: none;
    display: inline-flex;
    flex-flow: column nowrap;
    justify-content: center;
    align-items: center;
    overflow-y: hidden;
    /*background-color: deeppink;*/
    box-sizing: border-box;
    outline: none;
    width: 100%;
    /*max-width: 86%;*/
    font-size: 16px;
    font-family: 'Trebuchet MS', 'sans-serif';
    line-height: 18px;
    padding: 10px 4px 10px 10px;
    /*border-radius: 15px;*/
    color: #393939FF;
    border: none;
    background: transparent;
    transition: box-shadow .25s;
  }

  textarea::placeholder {
    /*font-family: 'Inter', 'sans-serif';*/
    color: rgba(57, 57, 57, 0.5);
    font-size: 16px;
  }
</style>