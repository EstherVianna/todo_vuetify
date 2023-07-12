<script setup>
import { reactive, watch, onBeforeMount } from 'vue';
import TaskPage from '../components/TaskPage.vue';

const state = reactive({
  task: "",
  tasks: [],
  hasTask: true,
  totalTasks: 0,
  completeTasks: 0
});

const handleAddTask = () => {
  if (state.task.trim() !== "") {
    state.tasks.push({
      content: state.task,
      complete: false,
      contentEditable: true
    });
    state.task = "";
    state.hasTask = true;
    ++state.totalTasks;
  } else {
    state.hasTask = false;
  }};

watch(() => state.tasks, (newTask) => {
  localStorage.setItem('task', JSON.stringify(newTask))
}, { deep: true });

watch(() => state.completeTasks, () => {
  localStorage.setItem('completeTasks', state.completeTasks)
});

watch(() => state.totalTasks, () => {
  localStorage.setItem('totalTasks', state.totalTasks);
});

const handleCompleteTask = (task) => {
  task.contentEditable = !task.complete;

  if (task.complete) {
    ++state.completeTasks;
  } else {
    --state.completeTasks;
  }};

const deleteTask = (index, task) => {
  state.tasks.splice(index, 1);

  if(task.complete){
    --state.completeTasks;
    --state.totalTasks;
  }else{
    --state.totalTasks
  }};

onBeforeMount(() => {
  const storedTasks = localStorage.getItem('task');
  const storedCompleteTasks = localStorage.getItem('completeTasks');
  const storedTotalTasks = localStorage.getItem('totalTasks');

  if (storedTasks !== null) {
    state.tasks = JSON.parse(storedTasks)
    state.completeTasks = parseInt(storedCompleteTasks)
    if (storedCompleteTasks < 0) {
      storedCompleteTasks = 0;
    }
    state.totalTasks = storedTotalTasks
  }
});
import { useTheme } from 'vuetify'

const theme = useTheme();
const toggleTheme = () => theme.global.name.value = theme.global.current.value.dark ? 'light' : 'dark';
</script>
<template>
<v-container>
    <v-card
    class="d-flex flex-column align-center"
    min-height="550px"
    max-width="1200px"
    elevation="8">
    <v-toolbar density="compact">
      <v-switch  prepend-icon="mdi-theme-light-dark"
         @change="toggleTheme()" color="success"/>
    </v-toolbar>
      <div class="d-flex flex-row ">
        <v-card-title 
          class="text-h6 text-md-h3 my-2 pa-2"
          >
          To dos
        </v-card-title>
          <v-container class="d-flex">
            <em class="ma-2 mr-4">Total
              <v-card-subtitle class="d-inline"
                v-if="state.totalTasks > 0">
              {{ state.totalTasks }}
              </v-card-subtitle>
            </em>
            <v-divider vertical inset></v-divider>
            <em class="ma-2">Done
              <v-card-subtitle class="d-inline"
                v-if="state.completeTasks > 0">
            {{ state.completeTasks }}
              </v-card-subtitle>
          </em>
          </v-container>
      </div>
      <v-form class="d-flex align-center" >
            <v-text-field 
              variant="outlined"
              clearable
              class="mx-4"
              size="60"
              v-model="state.task"
              @keypress.enter.prevent="handleAddTask">
            </v-text-field>
           <v-btn 
           variant="text"
            color="primary"
            @click.prevent="handleAddTask"
            > ADD
            <v-icon icon="mdi-plus"/>
          </v-btn>
       </v-form>
       <p error v-if="!state.hasTask"> Please, enter a task!</p>
      <v-list v-if="state.tasks.length > 0">
        <TaskPage v-for="task, index in state.tasks" 
                    :key="index" 
                    :task="task"
                    @add-complete-task="handleCompleteTask(task)"
                    @delete-task="deleteTask(index, task)"
                  />
      </v-list>
    </v-card>
</v-container>
</template>