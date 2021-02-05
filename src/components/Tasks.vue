<template>
  <div>
    <div class="hello">
      {{ msg }}
    </div>

    <div>
      <form @submit.prevent="addTask">
        <input
          type="text"
          value="formText"
          v-model="formText"
          placeholder="Add a task"
        />
        <button class="add-btn" type="submit">Add Task</button>
      </form>
      <small v-if="success">{{ success }}</small>
    </div>

    <section>
      <div v-if="taskList.length > 0">
        <ul v-for="task in taskList" :key="task.id">
          <li v-bind:class="{ complete: task.isComplete }">
            {{ task.text }}
            <button v-on:click="completeTask(task.id)">complete</button>
            <button v-on:click="deleteTask(task.id)">Delete</button>
            <button v-on:click="editTask(task)">Edit</button>
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "Tasks",
  props: {
    msg: String,
  },
  data() {
    return {
      formText: "",
      taskList: [],
      editing: false,
      activeTask: {},
      success: "",
    };
  },
  created() {
    const data = localStorage.getItem("VUE-TASKS");
    this.taskList = [...JSON.parse(data)];
  },
  methods: {
    addTask() {
      if (!this.editing) {
        const newTask = {
          id: Math.random().toString(),
          text: this.formText,
          isComplete: false,
        };
        this.taskList = [...this.taskList, newTask];
        this.formText = "";
        this.success = "Task has been added";
        const time = () =>
          setInterval(() => {
            this.success = "";
          }, 2000);
        time();
        clearInterval(time);

        localStorage.setItem("VUE-TASKS", JSON.stringify(this.taskList));
      } else if (this.editing) {
        this.taskList = this.taskList.map((task) =>
          task.id === this.activeTask.id
            ? { ...task, text: this.formText }
            : task
        );
        localStorage.setItem(
          "VUE-TASKS",
          JSON.stringify(
            this.taskList.map((task) =>
              task.id === this.activeTask.id
                ? { ...task, text: this.formText }
                : task
            )
          )
        );
        this.editing = !this.editing;
        this.formText = "";
      }
    },
    completeTask(id) {
      const taskIndex = this.taskList.findIndex((task) => task.id === id);
      const newList = [...this.taskList];
      newList[taskIndex] = {
        ...newList[taskIndex],
        isComplete: !newList[taskIndex].isComplete,
      };
      this.taskList = newList;
      localStorage.setItem("VUE-TASKS", JSON.stringify(newList));
    },
    deleteTask(id) {
      this.taskList = this.taskList.filter((task) => task.id !== id);
      localStorage.setItem(
        "VUE-TASKS",
        JSON.stringify(
          (this.taskList = this.taskList.filter((task) => task.id !== id))
        )
      );
    },
    editTask(task) {
      this.editing = !this.editing;
      this.activeTask = { ...task };
      this.formText = task.text;
      console.log(this.activeTask);
      console.log("editing", this.editing);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
.complete {
  text-decoration: line-through;
}
button {
  margin-right: 8px;
}
button:first-child {
  margin-left: 8px;
  background: green;
}

.add-btn {
  background: violet;
}
.hello {
  margin-bottom: 20px;
}
</style>
