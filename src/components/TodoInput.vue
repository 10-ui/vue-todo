<script setup lang="ts">
import { ref } from 'vue';
import { statuses } from '@/const/statuses';
const taskName = ref('');
const dueTo = ref('2023-01-01');
const isErrMsg = ref(false);
const onSubmit = (e: Event) => {
  if (!taskName.value || !dueTo.value) {
    isErrMsg.value = true;
    e.preventDefault();
    return;
  }
  const items = JSON.parse(
    localStorage.getItem('items') || '[]'
  );
  const newItem = {
    id: items.length,
    name: taskName.value,
    dueTo: dueTo.value,
    status: statuses.NOT_START,
    onEdit: false,
  };
  items.push(newItem);
  localStorage.setItem('items', JSON.stringify(items));
  taskName.value = '';
  dueTo.value = '2023-01-01';
};
</script>
<template>
  <div>
    <p v-if="isErrMsg">タスク・期限を入力してください</p>
    <form @submit="onSubmit">
      <label
        >Task name<input type="text" v-model="taskName"
      /></label>
      <label
        >Due to<input type="date" v-model="dueTo"
      /></label>
      <label
        ><input type="submit" value="Add todo"
      /></label>
    </form>
  </div>
</template>
