<template>
<div class="Board">
  <div class="ToolsPanel">
    <div class="ToolsPanel__Search">
      <input class="ToolsPanel__SearchInput" placeholder="Search activity..." type="text" v-model="searchQuery">
      <button class="Btn Btn-Search"></button>
    </div>
    <button class="Btn Btn-AddActivity" :style="btnAddActivityStyle" @click="addActivity"><span>Add activity</span></button>
  </div>
  <div class="Board__list">
    <activity class="ac" v-for="(activity, idx) in activitiesSet"
              @activity-removed="delActivity"
              @activity-updated="saveActivity"
              :key="activity.id"
              :idx="idx"
              :activity="activity">
    </activity>
    <span class="Board__Delimiter ac"></span>
    <span class="Board__Delimiter ac"></span>
    <span class="Board__Delimiter ac"></span>
  </div>
</div>
</template>


<script>
import Activity from "@/components/Activity"
import CustomTextArea from "@/components/TaskTextArea"
import {makeActivityPrimer} from "@/components/Activity"

export default {
  name: "board",
  components: {
    Activity, CustomTextArea
  },
  data() {
    return {
      searchQuery: "",
      activitiesId: 0,
      activities: [],
      paletteColors: ['#c388ea', '#ea88d8', '#888fea', '#88eacb', '#9fea88', '#eac188'],
      currentBtnColor: '#c388ea'

      // activitiesId: this.board.activitiesId,
      // activities: this.board.activities.slice(),
    }
  },

  mounted() {
    console.log('mounted')
    // this.activitiesId = this.board.activitiesId
    // this.activities = this.board.activities.slice()

    if (localStorage.getItem("activities")) {
      // if (localStorage.getItem("inp")) {
      console.log('hello local')
      try {
        this.activities = JSON.parse(localStorage.getItem("activities"))
        this.activitiesId = JSON.parse(localStorage.getItem("activitiesId"))
        // this.inp = JSON.parse(localStorage.getItem("inp"))
        console.log('parsed successfully')
      }
      catch (e) {
        console.log(e, '>>whoops<<')
        localStorage.removeItem("activities")
        localStorage.removeItem("activitiesId")
        // localStorage.removeItem("inp")
      }
    } else {
      localStorage.setItem("activities", JSON.stringify(this.activities))
      localStorage.setItem("activitiesId", JSON.stringify(this.activitiesId))
    }
    // } else localStorage.setItem("inp", JSON.stringify(this.inp))
  },

  methods: {
    randomizeBtnColor() {
      this.currentBtnColor = this.paletteColors[Math.floor(Math.random() * this.paletteColors.length)]
    },
    addActivity() {
      this.activities.push(makeActivityPrimer(this.activitiesId++, Date.now()))
      this.randomizeBtnColor()
      this.updateBoard()
    },

    saveActivity(activityData) {
      this.activities.splice(activityData.idx, 1, activityData.activity)
      this.updateBoard()
    },

    delActivity(activityIdx) {
      this.activities.splice(activityIdx, 1)
      this.updateBoard()
    },

    updateBoard() {
      // this.$emit('board-updated', {
      //   activitiesId: this.activitiesId,
      //   activities: this.activities.slice()
      // })
      localStorage.setItem("activities", JSON.stringify(this.activities))
      localStorage.setItem("activitiesId", JSON.stringify(this.activitiesId))
    }
  },

  watch: {
    searchQuery() {
      console.log(this.searchQuery, '<<<')
    },
  },

  computed: {
    btnAddActivityStyle() {
      return {
        borderColor: this.currentBtnColor
      }
    },
    activitiesSet() {
      return this.activities.filter((activity) => {
        return (activity.title.toLowerCase().includes(this.searchQuery)
        || activity.tasks.filter(task => task.body.toLowerCase().includes(this.searchQuery)).length > 0)
      })
    }
  }
}
</script>

<style scoped>
 .Board {
   width: 100%;
   /*height: 100%;*/
   padding: 16px 24px;
   overflow-x: hidden;
   display: flex;
   flex-flow: column nowrap;
   align-items: center;
 }

 .Board__list {
   /*flex: 1 0 auto;*/
   /*margin-top: 40px;*/
   display: flex;
   width: 96%;
   flex-flow: column wrap;
   align-content: flex-start;
   height: 600px;
   column-gap: 14px;
   row-gap: 28px;

 }

 .ToolsPanel {
   min-height: 40px;
   height: 40px;
   display: flex;
   flex-flow: row nowrap;
   margin-bottom: 32px;
   width: 40%;
   min-width: 320px;
 }

 .ToolsPanel__Search {
   position: relative;
   flex: 1 1 400px;
   background-color: #f8f8f8;

 }

 .ToolsPanel__SearchInput {
   font-family: 'Trebuchet MS', 'sans-serif';
   color: #242424;
   font-size: 16px;
   padding: 0 10px;
   width: 100%;
   height: 100%;
 }
 .ToolsPanel__SearchInput::placeholder {
   color: rgba(57, 57, 57, 0.5);
 }

 /* Re-order items into 3 rows */
 .ac:nth-of-type(4n+1) { order: 1; }
 .ac:nth-of-type(4n+2) { order: 2; }
 .ac:nth-of-type(4n+3) { order: 3; }
 .ac:nth-of-type(4n)   { order: 4; }

 .Board__Delimiter {
   flex-basis: 100%;
   width: 0;
   margin: 0;
   content: "";
   padding: 0;
 }

 .Btn-AddActivity {
   /*border: 2px solid transparent;*/
   margin-left: 20px;
   flex: 0 0 140px;
   display: flex;
   flex-flow: row nowrap;
   align-items: center;
   justify-content: center;
   background-color: white;
   cursor: pointer;
   color: #242424;
   font-size: 16px;
   font-weight: 600;
   font-family: 'Trebuchet MS', 'sans-serif';
 }

 .Btn-Search {
   position: absolute;
   top: calc(50% - 12px);
   right: 12px;
   background-image: url("../assets/img/SearchTool.svg");
   background-origin: content-box;
   background-repeat: no-repeat;
   background-position: center center;
   padding: 2px;
   background-size: 100%;
   width: 24px;
   height: 24px;
   background-color: transparent;
   /*cursor: pointer;*/
 }


</style>