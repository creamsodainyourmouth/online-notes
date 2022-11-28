<template>
  <div class="home">
<!--    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>15 TIMES REVERSED PROBLEMS<<<<<<<<<<<<<<<<<<<<<<<<<<<<<-->
    <board></board>
  </div>

</template>

<script>
// @ is an alias to /src
import Board from "@/components/Board"


export default {
  name: 'Home',
  components: {
    Board
  },
  data() {
    return {
      board: {
        activitiesId: 0,
        activities: [],
      },
      inp: ""
    }
  },

}
</script>

<style scoped>
  .home {
    display: flex;
    flex-flow: column nowrap;
    width: 100%;
    height: 100%;
    /*overflow-x: hidden;*/
  }
</style>
