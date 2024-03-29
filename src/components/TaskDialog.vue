<!--suppress ES6ShorthandObjectProperty -->
<template>

  <q-card class="dialog" style="max-width: 1500px;width:1000px;height:600px">
    <q-form
      @submit="handleSubmit">
      <q-list style="padding-left: 40px; padding-top: 40px">
        <q-item style="padding-bottom: 0 !important;">
          <q-input v-model="taskTitle"
                   :input-style="{ color: '#E3E5E5A1', lineHeight: '25px', width: '700px' }"
                   :rules="[val => val.length >= 5]"
                   autofocus
                   autogrow
                   borderless
                   class="new-task-name"
                   error-message="The title should have at least 5 characters!"
                   lazy-rules
                   no-error-icon
                   placeholder="Task Name"
          />
        </q-item>
        <q-item style="max-height: 10px; padding-bottom: 0">
          <q-input v-model="taskDate" :input-style="{ color: '#E3E5E5A1' }" borderless readonly>
            <template v-slot:before>
              <div class="event-icon">
                <q-icon clickable name="event" size="15px" style="margin-right: 5px;">
                  <q-popup-proxy transition-hide="scale" transition-show="scale">
                    <div class="row">
                      <q-date v-model="taskDate" color="indigo-10" dark mask="D MMMM YYYY - HH:mm">
                      </q-date>
                      <q-time v-model="taskDate" format24h mask="D MMMM YYYY - HH:mm">
                      </q-time>
                    </div>
                  </q-popup-proxy>
                </q-icon>
              </div>
              <span style="font-size:15px; color: #78909C; ">Due date:</span>
            </template>
          </q-input>
        </q-item>

        <q-item style="max-height: 10px; padding-top: 0">
          <q-input
            v-model.number="taskWeight"
            borderless
            color="secondary"
            dark
            style="max-width: 100px; min-height: 20px"
            type="number"
          >
            <template v-slot:before>
              <span style="font-size:15px; color: #78909C; ">Weight:</span>
            </template>
          </q-input>
        </q-item>
        <q-item style="padding-top: 20px;">
          <div style="font-size:20px; color: #78909C; ">Task type: <span style="color:#E3E5E5A1"> {{
              displayType
            }} </span>
          </div>
        </q-item>
        <q-item>
          <div style="display: grid; grid-template-columns: 1fr 1fr 1fr; grid-column-gap:20px; grid-row-gap: 10px">
            <TaskType
              v-for="(obj,name) in taskTypes"
              :key="name"
              :color="obj.color"
              :text="name"
              @click="taskType=name">
            </TaskType>
            <div style="width:25px; height: 25px;
                        background-color: rgba(227, 229, 229, 0.63);
                        border-radius: 5px; display: flex; justify-content: center; align-items: center">
              <q-icon name="img:/icons/add.svg" size="15px"></q-icon>
            </div>
          </div>
        </q-item>
        <q-item style="padding-top: 20px;">
          <q-input v-model="taskDescription"
                   :input-style="{color: '#E3E5E5A1',width: '500px' }"
                   :rules="[val => val.length >= 5]"
                   autofocus autogrow
                   borderless
                   class="description"
                   error-message="The description should have at least 5 characters!"
                   lazy-rules
                   no-error-icon
                   placeholder="Description..."
          />
        </q-item>
        <q-item style="padding-top: 10px;">
          <q-btn
            v-close-popup v-ripple text-color="secondary" type="submit">{{ buttonText }}
          </q-btn>
        </q-item>
      </q-list>
    </q-form>
    <q-btn v-close-popup class="close-button" dense icon="close" round side style="color:rgba(227, 229, 229, 0.63)">

    </q-btn>
  </q-card>
</template>


<style lang="scss" scoped>

.close-button {
  position: absolute;
  top: 10px;
  right: 10px
}

.dialog {

  background-color: #303030;
}

.new-task-name {
  font-size: 30px;
}

.event-icon {
  margin-bottom: 3px;
  color: #78909C;
}

.event-icon:hover {
  color: #8ea6af;
}

.description {
  font-size: 20px;
}

.q-field__messages {
  color: #660048 !important
}

.q-field__label {
  color: #660048 !important
}

.text-negative {
  color: #660048 !important
}
</style>
<script>
/* eslint-disable vue/no-mutating-props */
import TaskType from "../components/TaskType"
import {date} from "quasar";

export default {
  name: "AddTaskDialog",
  props: {
    edit: {
      type: Boolean,
    },
    task: {
      default: null,
    }
  },
  components: {
    TaskType,
  },
  mounted() {
    if (this.edit && this.task != null) {
      this.taskTitle = this.task.name;
      this.taskDate = this.task.time;
      this.taskType = this.task.icon;
      this.taskWeight = this.task.weight;
      this.taskDescription = this.task.description;
    }
  },
  data() {
    return {
      submitted: false,
      taskTypes: {
        Study: {
          color: "#78909C"
        },
        Music:{
          color: "#9C7890"
        },
        Hobby: {
          color: "#8A9C78"
        },
        Swim: {
          color: "#3B557D"
        },
        Cook:{
          color: "#5B533E"
        },
        Other:{
          color: "#B4B4B4"
        },
      },
      taskTitle: "",
      taskType: "Other",
      taskDescription: "",
      taskDate: date.formatDate(Date.now(), "D MMMM YYYY - HH:mm"),
      taskWeight: 1
    }
  },
  methods: {
    createTask() {
      return {
        name: this.taskTitle,
        description: this.description,
        icon: this.taskType,
        time: this.taskDate,
        weight: this.taskWeight
      }
    },
    addTask() {
      console.log("Adding a task!");
      this.$emit("addTask", this.createTask());
    },
    editTask() {
      const copy = JSON.parse(JSON.stringify(this.task));
      this.task.name = this.taskTitle
      this.task.time = this.taskDate;
      this.task.icon = this.taskType;
      this.task.weight = this.taskWeight
      this.task.description = this.taskDescription;
      if (this.shallowEqual(copy,JSON.parse(JSON.stringify(this.task)))  === false){
        console.log("Change", copy, JSON.parse(JSON.stringify(this.task)));
        this.$emit("editTask", this.task, this.createTask());
      }
    },
    shallowEqual(object1, object2) {
      const keys1 = Object.keys(object1);
      const keys2 = Object.keys(object2);
      if (keys1.length !== keys2.length) {
        return false;
      }
      for (let key of keys1) {
        if (object1[key] !== object2[key]) {
          return false;
        }
      }
      return true;
    },
    handleSubmit() {
      if (this.edit) {
        this.editTask();
      } else {
        this.addTask();
      }
    }
  },
  computed: {
    displayType() {
      if (this.taskType.length === 0) {
        return "Other";
      }
      return this.taskType;
    },
    buttonText() {
      if (this.edit) {
        return "Edit Task";
      }
      return "Create task";
    }
  }
}
</script>
