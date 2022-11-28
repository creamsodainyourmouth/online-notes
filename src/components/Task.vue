<template>
  <div class="Task">
    <div class="BodyPanel" :class="bodyPanelClass">
      <TaskCompleteBox v-if="initiated"
                class="BodyPanel__status"
                @checkbox-toggled="completeTask"
                :checked="completed"
                :icon-path="taskCompletedIconPath">
      </TaskCompleteBox>

      <div v-else class="BodyPanel__Add"></div>

<!--      <textarea v-if="editing"-->
<!--              maxlength="300"-->
<!--              ref="bodyInput"-->
<!--              @input="createTask"-->
<!--              @keyup.enter="finishEdit"-->
<!--              @focus="focusTask"-->
<!--              @blur="updateTask"-->
<!--              v-model="body"-->
<!--              :placeholder="placeholder"-->
<!--              class="BodyPanel__Text">-->
<!--      </textarea>-->

      <TaskTextArea v-if="editing" :content="body"
                    @content-updated="updateTaskBody"
                    @task-created="createTask"
                    @content-focused="focusTask"
                    @edit-finished="finishEdit"
                    @content-blurred="updateTask">
      </TaskTextArea>


      <div :class="taskBodyClass"
           v-else @dblclick="editTask"
           class="BodyPanel__content">
        {{ body }}
      </div>
      <div class="BtnClose" v-show="initiated" @click="removeTask"></div>

    </div>


    <div v-show="initiated" class="PriorityPanel">
      <select class="PriorityPanel__select" @change="updateTask"
              name="priority"
              id="priority"
              v-model="priority">
        <option class="PriorityPanel__option" v-for="option in priorityParams"
                :value="option.value">{{ option.text }}
        </option>
      </select>
    </div>
<!--    <div class="test">efeffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff</div>-->
  </div>
</template>


<script>
import TaskCompleteBox from "@/components/TaskCompleteBox"
import TaskTextArea from "@/components/TaskTextArea"
export function makeTaskPrimer(id, createdTime) {
  return {
    id: id,
    body: '',
    createdTime: createdTime,
    completed: false,
    initiated: false,
    focused: false,
    editing: true,
    priority: 1,
    sense: 1
  }
}
export default {
  name: "task",
  components: {
    TaskCompleteBox, TaskTextArea
  },
  props: {
    task: {
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
      id: this.task.id,
      body: this.task.body,
      completed: this.task.completed,
      editing: this.task.editing,
      initiated: this.task.initiated,
      createdTime: this.task.createdTime,
      priority: this.task.priority,
      focused: this.task.focused,
      // sense: this.task.sense,
      priorityParams: null,
      placeholder: "New task...",
      // taskCompletedIconPath: 'yess10.svg'
      taskCompletedIconPath: 'yesKa.png'
    }
  },

  created() {
    this.priorityParams = this.createPriorityParams()
  },

  computed: {
    sense() {
      console.log('sensensennsesnesnenesnenesnenesnens')
      return 1 / this.priority
    },
    taskBodyClass() {
      return {
        "Task__body--completed": this.completed,
      }
    },

    bodyPanelClass() {
      return {
        "BodyPanel--primer": !this.initiated,
        "BodyPanel--focused": this.focused,
      }
    }
  },

  methods: {
    updateTaskBody(taskBody) {
      this.body = taskBody
    },
    completeTask() {
      this.completed = !this.completed
      this.updateTask()
    },

    focusTask() {
      this.focused = true
    },

    createPriorityParams() {
      return [
        { text: 'Приоритет 1', value: 1},
        { text: 'Приоритет 2', value: 2},
        { text: 'Приоритет 3', value: 3},
        { text: 'Приоритет 4', value: 4},
        { text: 'Приоритет 5', value: 5},
      ]
    },

    editTask() {
      this.editing = true
      setTimeout(()=>{
        console.log(this.$children[1].$refs.textarea.focus(), '<<<chi')
        // this.$refs.bodyInput.focus()
      },10)
    },

    finishEdit() {
      if (!this.initiated) return
      this.editing = false
    },

    createTask() {
      if (!this.initiated) {
        console.log('adding task')
        this.$emit('task-created')
        this.initiated = true
        this.placeholder = false
      }
    },

    updateTask() {
      console.log('updating task')
      this.finishEdit()
      this.focused = false
      this.$emit('task-updated', {
        idx: this.idx,
        task: {
          id: this.id,
          body: this.body,
          createdTime: this.createdTime,
          completed: this.completed,
          initiated: this.initiated,
          editing: this.editing,
          priority: this.priority,
          focused: this.focused,
          sense: this.sense,
        }
      })
    },

    removeTask() {
      this.$emit('task-removed', this.idx)
    },
  },
}
</script>


<style scoped>

  .Task__body--completed {
    text-decoration: line-through;
  }

  .Task {
    display: flex;
    flex-flow: column nowrap;
    width: 100%;
    height: auto;
    padding: 6px 0;
  }

  .BodyPanel {
    box-sizing: border-box;
    display: flex;
    flex-flow: row nowrap;
    align-items: flex-start;
    /*justify-content: flex-start;*/
    width: 100%;
    height: 100%;
    padding: 0 10px 0 14px;
    border-bottom: 1px solid transparent;
    border-top: 1px solid transparent;
  }

  .BodyPanel__status, .BodyPanel__Add {
    /*margin: 14px 10px 14px 14px;*/
    margin-top: 10px;
  }

  .BodyPanel--focused {
    border-bottom: 1px solid #393939FF;
    border-top: 1px solid #393939FF;
  }

  .BodyPanel__content {
    overflow: auto;
    height: auto;
    flex-flow: column nowrap;
    text-align: start;
    overflow-wrap: break-word;
    display: inline-flex;
    flex-flow: column nowrap;
    width: 86%;
    font-size: 16px;
    color: #242424;
    /*font-family: 'Trebuchet MS', 'sans-serif';*/
    line-height: 18px;
    padding: 10px 4px 10px 10px;
    border: none;
    background: transparent;
    transition: box-shadow .25s;
  }

  .BtnClose {
    background-image: url("../assets/img/xClose.svg");
    background-size: 12px 12px;
    background-repeat: no-repeat;
    background-position: center center;
    flex-basis: 24px;
    flex-shrink: 0;
    height: 24px;
    width: 24px;
    /*border-radius: 50%;*/
    margin-top: 6px;
    transition: .25s ease-in-out;
    cursor: pointer;
    border: 2px solid transparent;
    /*transition: background-color .25s;*/
  }

  .BtnClose:hover {
    border: 2px solid rgba(255, 255, 255, .5);
    transition: .25s ease-in-out;
    /*background-image: url("../assets/img/xClose--hover.svg");*/
    /*background-color: rgba(255, 255, 255, .3);*/
  }

  .BodyPanel__Add {
    background-image: url("../assets/img/xClose.svg");
    background-size: 11px 11px;
    background-repeat: no-repeat;
    background-position: center center;
    flex-basis: 14px;
    flex-shrink: 0;
    height: 14px;
    width: 14px;
    margin-top: 10px;
    transform: rotate(45deg);
  }


  .PriorityPanel {
    /*position: relative;*/
    padding: 0 40px;
    display: inline-flex;
    flex-flow: row nowrap;
    justify-content: flex-start;
  }

  .PriorityPanel__select {
    text-transform: full-size-kana;
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

  .PriorityPanel__select:hover {
    border: 2px solid rgba(255, 255, 255, .6);
  }

  .PriorityPanel__option {
    background-color: #ffffff;
  }


</style>