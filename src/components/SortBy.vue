<template>
<div class="SortBy SortPanel">
<!--  <span>Сортировка</span>-->
  <select class="SortPanel__Select" @change="genSortEvent"
          v-model="sortBy"
          name="sortBy" id="sortBy">
    <option v-for="option in sortParams.options"
            :value="option.value">{{ option.text }}
    </option>
  </select>
  <div class="Toggle">
    <OrderToggleSorting @toggle-enabled="reverseItems" :toggled="reversed"></OrderToggleSorting>
    <!--    <span>{{ orderTitle }}</span>-->
  </div>

</div>
</template>


<script>
import CheckBox from "@/components/TaskCompleteBox"
import OrderToggleSorting from "@/components/OrderToggleSorting"
export default {
  name: "SortBy",
  components: {
    OrderToggleSorting,
  },

  props: {
    sortParams: {
      type: Object,
      required: true,
    }
  },

  data() {
    return {
      sortBy: this.sortParams.sortBy,
      reversed: this.sortParams.reversed,
      iconNormalPath: "SortingOrder.png",
      iconActivePath: "SortingOrder--normal.png",
    }
  },

  computed: {
    orderTitle() {
      if (this.reversed) return "По убыванию"
      return "По возрастанию"
    }
  },

  methods: {
    reverseItems() {
      this.reversed = !this.reversed
      this.genSortEvent()
    },

    genSortEvent() {
      this.$emit('items-sorted', this.sortBy, this.reversed)
    },
  }
}
</script>


<style scoped>
  .SortPanel {
    display: flex;
    flex-flow: row nowrap;
  }

  .SortPanel__Select {
    margin-right: 8px;
    padding: 0 2px;
    font-size: 12px;
    display: flex;
    flex-flow: row nowrap;
    justify-content: center;
    height: 20px;
    border: 2px solid #242424;
    transition: border-color 0.25s;
    /*background-color: rgba(255, 255, 255, .5);*/
    background-color: inherit;
    cursor: pointer;
  }
</style>