<template>
  <div class="OrderToggle" :title="title">
    <label class="CovertToggle" :class="toggleClass">
      <span class="CovertToggle__Icon" :style="toggleStyle"></span>
      <input @change="toggle" type="checkbox" class="InputCheckBox" v-model="isToggled">
    </label>
  </div>
</template>

<script>
export default {
  name: "OrderToggleSorting",
  props: {
    toggled: {
      type: Boolean,
      required: true,
    }
  },

  data() {
    return {
      isToggled: this.toggled,
      iconAsc: "SortingOrder--asc.png",
      iconDesc: "SortingOrder--desc.png"
    }
  },

  computed: {
    title() {
      if (this.isToggled) return "По убыванию"
      return "По возрастанию"
    },

    toggleClass() {
    },

    toggleStyle() {
      let currentIcon

      this.isToggled ? currentIcon = this.iconDesc: currentIcon = this.iconAsc
      return {
        backgroundImage: 'url(' + require(`@/assets/img/${currentIcon}`) + ')'
      }
    }
  },

  methods: {
    toggle() {
      this.$emit('toggle-enabled')
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
  .OrderToggle {
    padding-top: 1px;
  }

  label {
    display: inline-flex;
    align-items: center;
    user-select: none;
  }

  .CovertToggle__Icon {
    display: flex;
    width: 18px;
    height: 18px;
    /*flex-shrink: 0;*/
    /*flex-grow: 0;*/
    cursor: pointer;
    transition: .25s ease-in-out;
    background-repeat: no-repeat;
    background-position: 2% 2%;
    background-size: 100% 100%;
    background-origin: content-box;
}

</style>