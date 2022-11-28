<template>
  <div class="CheckBox">
    <label class="CovertCheckBox" :class="checkBoxClass">
      <span class="CovertCheckBox__Icon" :style="checkBoxStyle"></span>
      <input @change="toggle" type="checkbox" class="InputCheckBox" v-model="isChecked">
    </label>

  </div>
</template>

<script>
export default {
  name: "CheckBox",
  props: {
    checked: {
      type: Boolean,
      required: true,
    },
    iconPath: {
      type: String,
    },
  },

  data() {
    return {
      isChecked: this.checked
    }
  },

  computed: {
    checkBoxClass() {
      return {
        "CovertCheckBox--checked": this.checked
      }
    },

    checkBoxStyle() {
      // howwwwwwwwwwww
      // let b = {backgroundImage: 'url(' + this.iconPath + ')'}
      // let c = { backgroundImage: `url(@/assets/img/${this.iconPath}) + ')` }

      // let style = {
      //   width: `${this.boxSize}px`,
      //   height: `${this.boxSize}px`
      // }

      // let style = {
      //   backgroundImage: 'url(' + require(`@/assets/img/${this.iconPath}`) + ')'
      // }
      // if (this.twiceMode) {
      //   style.backgroundImage = 'url(' + require(`@/assets/img/${this.iconPath}`) + ')'
      // }
      // if (this.iconPath && this.checked) {
      //   return {
      //     backgroundImage: 'url(' + require(`@/assets/img/${this.iconPath}`) + ')',
      //     backgroundRepeat: 'no-repeat',
      //     backgroundPosition: 'center center',
      //     backgroundSize: '100%',
      //     backgroundOrigin: 'content-box',
      //   }
      // }
    }
  },

  methods: {
    toggle() {
      this.$emit('checkbox-toggled')
    },
  }
}
</script>

<style scoped>
  .InputCheckBox {
    position: absolute;
    opacity: 0;
    z-index: -1;
  }

  label {
    display: inline-flex;
    align-items: center;
    user-select: none;
  }


  .CovertCheckBox > span {
    display: flex;
    width: 16px;
    height: 16px;
    /*flex-shrink: 0;*/
    /*flex-grow: 0;*/
    border: 2px solid #242424;
    cursor: pointer;
    transition: border-color .25s;
    background-repeat: no-repeat;
    background-position: 90% 80%;
    background-size: 100% 100%;
    background-origin: content-box;
  }

  .CovertCheckBox:hover > span {
    border-color: rgba(255, 255, 255, .6);
  }

  .CovertCheckBox--checked > span {
    background-image: url("../assets/img/yesKa.svg");
    background-size: 99%;
    background-position: center center;
    background-repeat: no-repeat;
    background-origin: content-box;

  }

  .CovertCheckBox--checked:hover > span {
    border: 2px solid #393939;
  }


</style>