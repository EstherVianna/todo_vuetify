<script setup>
import { ref } from 'vue'

defineProps({
  task:{
    type: Object,
    required: true
  }
});

const labelRef = ref(null);
const focusLabel= () => {
    labelRef.value.focus();
};

</script>
<template>
<v-list 
 class="d-flex justify-center align-center"
 density="compact"
>
    <v-checkbox 
     class="pe-2" color="success"
     v-model="task.complete"
     @change="$emit('add-complete-task', task)">
    </v-checkbox>
    <label ref="labelRef" >
        <v-text-field  type="text"
         size="40"
         variant="outlined"
         v-model="task.content"
         :class="{ 'task-item-completed':task.complete }">
        </v-text-field>
    </label>
    <v-list-item
     align-center min-width="80px">
        <v-icon
         icon="mdi-delete-outline"
         variant="plain"
         @click="$emit('deleteTask')"/>
        <v-icon
         icon="mdi-pencil-outline"
         v-show="task.contentEditable"
         @click.prevent="focusLabel"/>
    </v-list-item>
</v-list>
</template>
<style>
.task-item-completed{
    text-decoration: line-through;
}
</style>