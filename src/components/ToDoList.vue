<template>
  <div class="todo-container">
    <h1>Hammam CRUD</h1>
    <input
      type="text"
      id="taskName"
      name="taskName"
      v-model="newTask"
      @keyup.enter="addTask"
      placeholder="Add a new task"
      class="todo-input"
      autocomplete="off"
      ref="inputRef"
    />

    <ul>
      <li v-for="(task, index) in tasks" :key="index" class="task-item">
        <label :for="'task-checkbox-' + index" class="task-label">
          <input
            type="checkbox"
            :id="'task-checkbox-' + index"
            v-model="task.completed"
            class="task-checkbox"
            @click="toggleTaskCompletion(task)"
          />
          <span
            v-if="!task.isEditing"
            :class="{ completed: task.completed }"
            @dblclick="editTask(task)"
            @click="toggleTaskCompletion(task)"
          >
            {{ task.name }}
          </span>
          <input
            v-else
            v-model="task.name"
            @keyup.enter="saveTaskEdit(task)"
            @blur="saveTaskEdit(task)"
            class="todo-edit-input"
          />
        </label>
        <div class="task-buttons">
          <button @click="removeTask(index)">Delete</button>
          <button v-if="!task.isEditing" @click="editTask(task)">Edit</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: "",
      tasks: JSON.parse(localStorage.getItem("tasks")) || [],
    };
  },
  computed: {
    completedTasks() {
      return this.tasks.filter((task) => task.completed);
    },
  },
  watch: {
    newTask(newVal, oldVal) {
      console.log(`Task changed from ${oldVal} to ${newVal}`);
    },
    tasks: {
      handler(newTasks) {
        // Simpan tasks ke localStorage setiap kali ada perubahan
        localStorage.setItem("tasks", JSON.stringify(newTasks));
      },
      deep: true, // Pastikan untuk mendeteksi perubahan dalam objek atau array
    },
  },

  methods: {
    addTask() {
      if (this.newTask.trim()) {
        this.tasks.push({
          name: this.newTask,
          completed: false,
          isEditing: false,
        });
        this.newTask = "";
      }
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
    },
    editTask(task) {
      task.isEditing = true;
    },
    saveTaskEdit(task) {
      if (task.name.trim()) {
        task.isEditing = false;
      } else {
        this.removeTask(this.tasks.indexOf(task));
      }
    },
    toggleTaskCompletion(task) {
      task.completed = !task.completed;
    },
  },
  mounted() {
    this.$nextTick(() => {
      this.$refs.inputRef.focus();
    });
  },
};
</script>

<style scoped>
.todo-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f8f9fa;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.todo-container h1 {
  font-size: 24px;
  margin-bottom: 20px;
  color: #333;
}

.todo-input {
  width: calc(100% - 40px);
  padding: 10px 15px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  outline: none;
}

.todo-input::placeholder {
  color: #aaa;
}

.todo-input:focus {
  border-color: #66afe9;
}

ul {
  padding: 0;
  margin: 0;
}

.task-item {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.task-label {
  display: flex;
  align-items: center;
}

.task-checkbox {
  margin-right: 10px;
  appearance: none;
  width: 20px;
  height: 20px;
  border: 2px solid #ccc;
  border-radius: 4px;
  cursor: pointer;
}

.task-checkbox:checked {
  background-color: #66afe9;
  border-color: #66afe9;
}

.task-checkbox:focus {
  outline: none;
  box-shadow: 0 0 0 3px rgba(102, 175, 233, 0.5);
}

.task-label input[type="checkbox"] + span {
  flex: 1;
  word-wrap: break-word;
  padding: 10px 0;
}

.task-label input[type="checkbox"]:checked + span {
  color: #aaa;
  text-decoration: line-through;
}

.task-buttons button {
  margin-left: 10px;
  padding: 8px 15px;
  font-size: 14px;
  color: #fff;
  background-color: #dc3545;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.task-buttons button:hover {
  background-color: #c82333;
}
.task-item {
  display: flex;
  align-items: center;
  justify-content: space-between; /* Menambahkan ini untuk menyelaraskan konten */
  margin-bottom: 10px;
}

.task-label {
  display: flex;
  align-items: center;
  flex: 1; /* Menambahkan ini agar label mengambil ruang yang tersedia */
}

.task-buttons {
  display: flex; /* Menambahkan ini untuk menyelaraskan tombol */
}
</style>
