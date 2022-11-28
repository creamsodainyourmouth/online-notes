<template>
  <div class="Activity" :style="activityStyle">
    <div class="Activity__RemovePanel">
      <button class="Btn Btn--RemoveActivity" :style="btnRemoveStyle" @click="removeActivity"></button>
    </div>
    <div class="Activity__ControlPanel">
      <div class="Activity__CreatedDate">{{ stringCreatedDate }}</div>
      <div class="Palette">
        <div class="Palette__Colors">
          <div v-for="color in paletteColors"
               @click="colorizeActivity(color)"
               :style="paletteColor(color)"
               class="Palette__color">
          </div>
        </div>
      </div>
      <div class="Activity__Progress">{{ completeness }}</div>
      <SortBy class="Sorting" @items-sorted="updateSortParams"
              :sort-params="sortParams">
      </SortBy>
    </div>

    <div class="Activity__TitlePanel">
      <input class="Activity__Title"
             @blur="updateActivity"
             placeholder="New activity..."
             type="text"
             v-model="title" maxlength="26">
    </div>

    <div class="Activity__TasksList">
      <Task v-for="(task, idx) in tasks"
            @task-updated="saveTask"
            @task-created="addTask"
            @task-removed="delTask"
            :task="task"
            :key="task.id"
            :idx="idx">
      </Task>
    </div>
  </div>
</template>


<script>
import Task from "@/components/Task"
import SortBy from "@/components/SortBy"
import {makeTaskPrimer} from "@/components/Task"

function byField(field) {
  return (left, right) => left[field] > right[field] ? 1: -1
}

export function makeActivityPrimer(id, createdDate) {
  return {
    id: id,
    title: "",
    createdDate: createdDate,
    completeness: null,
    completed: false,
    initiated: false,
    reversed: false,
    sortBy: "createdTime",
    color: "#c388ea",
    tasksId: 0,
    tasks: []
  }
}

export default {
  name: "activity",
  components: {
    Task, SortBy
  },
  props: {
    activity: {
      type: Object,
      required: true,
    },
    idx: {
      type: Number,
      required: true,
    }
  },

  data() {
    return {
      tasksId: this.activity.tasksId,
      id: this.activity.id,
      completed: this.activity.completed,
      title: this.activity.title,
      // completeness: this.activity.completeness,
      initiated: this.activity.initiated,
      color: this.activity.color,
      sortBy: this.activity.sortBy,
      createdDate: this.activity.createdDate,
      reversed: this.activity.reversed,
      sortParams: null,
      paletteColors: ['#c388ea', '#ea88d8', '#888fea', '#88eacb', '#9fea88', '#eac188'],

      // Это вовсе не локальный объект, полностью ссылочный! Любые его изменения здесь
      // будут приводить к изменениям в родительском компоненте!!! Будто мы напрямую
      // изменяем props!!!
      // tasks: this.activity.tasks

      tasks: this.activity.tasks.slice(),
    }
  },

  created() {
    if (!this.tasks.length) {
      // this.tasks.push(makeTaskPrimer(this.tasksId++, new Date()))
      this.tasks.push(makeTaskPrimer(this.tasksId++, Date.now()))
      this.updateActivity()
    }
    this.sortParams = this.createSortParams()
  },

  mounted() {
    if (this.tasks.length === 1) {
      console.log(this.$children, 'choooooooo')
      // console.log(this.$children[1].$refs.bodyInput.focus())
    }
    if (this.tasks) {
      console.log('check sense')
      console.log(this.tasks, '<<<<')
    }
  },

  computed: {
    completeness() {
      if (this.tasks.length) {
        let res = 0
        let senseTasks = this.tasks.slice(0, -1)
        let generalSense = senseTasks.reduce((accum, nextValue)=> accum + nextValue.sense, 0)
        let completedSense = senseTasks.filter(
            task => task.completed).reduce((accum, nextValue)=> accum + nextValue.sense, 0)
        if (completedSense) {
          res = completedSense / generalSense * 100
          this.completed = res === 100
          if (!Number.isInteger(res)) res = res.toFixed(2)
        }
        return `${res}%`
      }
    },

    stringCreatedDate() {
      return new Date(this.createdDate).toDateString().slice(0, -5)
    },
    activityStyle() {
      return {
        backgroundColor: this.color,
      }
    },

    btnRemoveStyle() {
      return {
        backgroundColor: this.color
      }
    }
  },


  watch: {
    completed() {
      let deltaTime = Date.now() - this.createdDate
    }
  },

  methods: {
    createSortParams() {
      return {
        options: [
          {text: "Дата добавления", value: "createdTime"},
          {text: "По алфавиту", value: "body"},
          {text: "Приоритет", value: "priority"},
        ], reversed: this.reversed, sortBy: this.sortBy
      }
    },

    colorizeActivity(color) {
      console.log('aadsd color>>>', color)
      this.color = color
      this.updateActivity()
    },

    paletteColor(color) {
      return {
        backgroundColor: color
      }
    },

    sortTask() {
      // Поскольку новая задача всегда пушится, то ее можно взять из конца
      let primer = this.tasks.pop()
      this.tasks.sort(byField(this.sortBy))
      if (this.reversed) this.tasks.reverse() // <<<<<<<<<<<a<a<A<a<A<<<<<<aaaaaaa<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
      // Чтобы праймер оставался всегда в конце
      this.tasks.push(primer)
    },

    updateSortParams(sortBy, reversed) {
      this.reversed = reversed
      this.sortParams.reversed = reversed


      if (sortBy === this.sortParams.options[0].value) {
        this.sortBy = "createdTime"
      }
      else if (sortBy === this.sortParams.options[1].value) {
        this.sortBy = "body"
      }
      else if (sortBy === this.sortParams.options[2].value) this.sortBy = "priority"

      this.sortTask()
      this.updateActivity()
    },

    removeActivity() {
      this.$emit('activity-removed', this.idx)
    },

    addTask() {
      console.log('добавли в активити')
      this.tasks.push(makeTaskPrimer(this.tasksId++,  Date.now()))
      // this.tasks.push(makeTaskPrimer(this.tasksId++,  new Date())) // <<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
      this.updateActivity()
    },

    saveTask(taskData) {
      console.log('saving task')
      if (!taskData.task.body && taskData.task.initiated) {
        console.log('need del empty task with id>>>', taskData.idx)
        this.delTask(taskData.idx)
      } else {
        this.tasks.splice(taskData.idx, 1, taskData.task)
      }
      // Сортируем здесь, чтобы после потери фокуса задача вставлялась в нужное место
      this.sortTask()
      this.updateActivity()
    },

    delTask(taskIdx) {
      this.tasks.splice(taskIdx, 1)
      this.updateActivity()
    },

    updateActivity() {
      this.$emit('activity-updated', {
        idx: this.idx,
        activity: {
          id: this.id,
          title: this.title,
          completed: this.completed,
          completeness: this.completeness,
          createdDate: this.createdDate,
          initiated: this.initiated,
          color: this.color,
          reversed: this.reversed,
          sortBy: this.sortBy,
          // Здесь мы!!! Также должны передавать новый объект контейнера!!! Иначе родитель
          // будет хранить !ссылку! на tasks из ребенка, и изменения в последнем будут проявляться в родителе
          // Board перестает быть единственным источником правды
          tasksId: this.tasksId,
          tasks: this.tasks.slice()
        }
      })
    }
  }
}
</script>


<style scoped>
  .Activity {
    position: relative;
    display: flex;
    flex-flow: column nowrap;
    max-width: 320px;
    height: auto;
    padding-bottom: 8px;
    /*margin-right: 32px;*/
    /*margin-bottom: 20px;*/
    transition: background-color .25s;
    font-family: 'Trebuchet MS', 'sans-serif';
    font-size: 14px;
  }

  .Activity__ControlPanel {
    /*display: flex;*/
    /*flex-flow: column nowrap;*/
    /*align-items: flex-start;*/
    display: grid;
    /*grid-template-areas:*/
    /*"Palette Progress"*/
    /*"Sorting .";*/
    grid-template-areas: "Palette CreatedDate"
                         "Sorting Progress";
    grid-template-rows: 1fr 1fr;
    grid-auto-columns: 1fr 1fr;
    grid-row-gap: 6px;
    padding: 12px 14px 14px 14px;
  }

  .Activity__RemovePanel {
  }

  .Btn--RemoveActivity {
    position: absolute;
    background-image: url("../assets/img/xClose.svg");
    background-size: 11px 11px;
    background-repeat: no-repeat;
    background-position: center center;
    height: 30px;
    width: 30px;
    border-radius: 50%;
    top: -10px;
    right: -10px;
    cursor: pointer;
    border: 2px solid #242424;
    /*cursor: pointer;*/
    /*color: rgba(36, 36, 36, .2);*/
    /*height: 20px;*/
    transition: border-color 0.25s;
    /*transition: background-color 0.25s;*/
  }

  .Btn--RemoveActivity:hover {
    background-color: #e2e2e2;
    /*border: 2px solid #E2E2E2FF;*/
    border: 2px solid rgba(255, 255, 255, .6);
    /*position: absolute;*/
    /*content: "";*/
    /*color: #242424;*/
  }

  .Btn--RemoveActivity:after {
    /*content: "";*/
    /*position: absolute;*/
    /*background: rgba(0, 0, 0, .2);*/
    /*border-radius: 50%;*/
    /*width: 20px;*/
    /*height: 20px;*/
    /*left: 0;*/
    /*top: 0;*/
  }

  .Activity__CreatedDate {
    grid-area: CreatedDate;
    font-size: 12px;
    color: #242424;
    display: flex;
    flex-flow: column;
    justify-content: center;
  }

  .Activity__Progress {
    grid-area: Progress;
    display: flex;
    flex-flow: column nowrap;
    justify-content: center;
    font-size: 18px;
    font-weight: 600;
    color: #242424;
  }

  .Activity__TitlePanel {
    padding: 0 14px;
    display: flex;
    flex-flow: row nowrap;
    margin-top: 8px;
    width: 100%;
  }

  .Activity__Title {
    width: 100%;
    font-size: 18px;
    font-weight: 600;
    color: #242424;
  }

  .Activity__Title::placeholder {
    font-family: 'Trebuchet MS', 'sans-serif';
    color: #242424;
  }

  .Palette {
    grid-area: Palette;
    display: flex;
    flex-flow: column nowrap;
    justify-content: center;
  }

  .Palette__Colors {
    display: grid;
    grid-gap: 6px;
    /*border: 2px solid #ededed;*/
    grid-template: repeat(1, 20px) / repeat(6, 20px);
    /*background-color: #ededed;*/
  }

  .Palette__color {
    border: 2px solid #242424;
    transition: border-color .25s;
    cursor: pointer;
  }

  .Palette__color:hover {
    border-color: rgba(255, 255, 255, .6);
  }

  .Sorting {
    grid-area: Sorting;
    display: flex;
    flex-flow: row nowrap;
    align-items:  center;
  }
</style>