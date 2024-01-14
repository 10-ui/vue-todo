<script setup lang="ts">
import { statuses } from '@/const/statuses';
import { ref } from 'vue';
let items = ref(
  JSON.parse(localStorage.getItem('items') || '[]')
);
let taskName = ref('');
let dueTo = ref('');
let status = ref();
let isErrMsg = ref(false);
let isShowModal = ref(false);
let deleteId = Number('');
let deleteItemContent = ref('');
let errMsg = ref('');
const onEdit = (id: number) => {
  let isOnEditOther = false;
  items.value.forEach(
    (item: { onEdit: boolean }, index: number) => {
      if (item.onEdit && index !== id) {
        isOnEditOther = true;
      }
    }
  );
  if (isOnEditOther) {
    errMsg.value = '他のタスクを編集中です';
    isErrMsg.value = true;
    return;
  } 
  taskName.value = items.value[id].name;
  dueTo.value = items.value[id].dueTo;
  status.value = items.value[id].status;
  items.value[id].onEdit = true;
};
const onUpdate = (id: number) => {
  if (!taskName.value || !dueTo.value) {
    errMsg.value = 'タスク・期限を入力してください';
    isErrMsg.value = true;
    return;
  }
  const updateItem = {
    id: id,
    name: taskName.value,
    dueTo: dueTo.value,
    status: status.value,
    onEdit: false,
  };
  items.value.splice(id, 1, updateItem);
  localStorage.setItem(
    'items',
    JSON.stringify(items.value)
  );
  isErrMsg.value = false;
};
const showModal = (id: number) => {
  deleteId = id;
  deleteItemContent = items.value[id].name;
  isShowModal.value = true;
};
const onDeleteItem = () => {
  items.value.splice(deleteId, 1);
  items.value = items.value.map(
    (
      item: {
        name: any;
        dueTo: any;
        status: any;
        onEdit: any;
      },
      index: number
    ) => ({
      id: index,
      name: item.name,
      dueTo: item.dueTo,
      status: item.status,
      onEdit: item.onEdit,
    })
  );
  localStorage.setItem(
    'items',
    JSON.stringify(items.value)
  );
  isShowModal.value = false;
};
const onHIdeModal = () => {
  isShowModal.value = false;
};
</script>
<template>
  <div>
    <p v-if="isErrMsg">{{ errMsg }}</p>
    <table>
      <tr>
        <th class="th-id">ID</th>
        <th class="th-name">Name</th>
        <th class="th-dueto">Due to</th>
        <th class="th-status">Status</th>
        <th class="th-action">Action</th>
        <th class="th-delete">Delete</th>
      </tr>
      <tr v-for="item in items" :key="item.id">
        <td>{{ item.id }}</td>
        <td>
          <span v-if="!item.onEdit">{{ item.name }}</span
          ><input v-else type="text" v-model="taskName" />
        </td>
        <td>
          <span v-if="!item.onEdit">{{ item.dueTo }}</span
          ><input v-else type="date" v-model="dueTo" />
        </td>
        <td>
          <span v-if="!item.onEdit">{{
            item.status.name
          }}</span>
          <select v-else v-model="status">
            <option
              v-for="status in statuses"
              :key="status.id"
              :value="status"
              :selected="status.id === item.status.id">
              {{ status.name }}
            </option>
          </select>
        </td>
        <td>
          <button
            @click="onEdit(item.id)"
            v-if="!item.onEdit">
            編集
          </button>
          <button @click="onUpdate(item.id)" v-else>
            完了
          </button>
        </td>
        <td>
          <button @click="showModal(item.id)">削除</button>
        </td>
      </tr>
    </table>
    <div v-if="isShowModal" class="modal">
      <div class="m-content">
        <p>Are you sure? {{ deleteItemContent }}</p>
        <button @click="onDeleteItem()">Yes</button>
        <button @click="onHIdeModal()">No</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}
.m-content {
  background: #fff;
  padding: 20px;
  border-radius: 8px;
}
</style>
