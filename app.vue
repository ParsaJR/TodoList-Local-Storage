<script setup lang="ts">
import { EditModal } from '#components'
const newTodoTitle = ref("");
const columns = [
  {
    key: 'id',
    label: '#'
  },
  {
    key: 'Title',
    label: 'Title'
  },
  {
    key: 'Status',
    label: 'Status',
    sortable: true
  },
  {
    key: 'Actions',
    label: 'Actions',
    class: 'text-center'
  }
];
const defaultTodos = ref([{
  id: 1,
  Title: 'Pay for Hosting',
  Status: 'In Progress',
  Actions: { class: 'text-center' }
},
{
  id: 2,
  Title: 'Meet Juliet',
  Status: 'Completed',
  Actions: { class: 'text-center' }
},
{
  id: 3,
  Title: 'Buying an Engagement Ring',
  Status: 'Completed',
  Actions: { class: 'text-center' }
}
])
const todos = ref();
OnPageLoad()
function OnPageLoad() {
  if (process.client) {
    console.log('process client pass');
    if (localStorage.hasOwnProperty('savedtodos')) {
      const savedtodos = localStorage.getItem('savedtodos');
      console.log('hasownpropery pass');
      if (savedtodos) {
        console.log("now setting todos to savedtodos");
        todos.value = JSON.parse(savedtodos);
      }
    }
    else {
      todos.value = defaultTodos.value;
    }
  }
}
function SaveOnLocalStorage() {
  localStorage.setItem('savedtodos', JSON.stringify(todos.value))
}


function RemoveFromArrayById(id: number) {
  todos.value = todos.value.filter(item => item.id !== id);
  SaveOnLocalStorage()
}
function EditTitleById(id: number, newTitle: string) {
  const todoIndex = todos.value.findIndex(item => item.id === id);
  todos.value[todoIndex].Title = newTitle;
  SaveOnLocalStorage()
}
function AddToArray() {
  const title = newTodoTitle.value;
  let maxId = 0;
  if (Object.keys(todos.value).length != 0) {
    maxId = Math.max(...todos.value.map(item => item.id));
  }
  console.log(maxId);
  const newTodo = {
    id: maxId + 1,
    Title: title,
    Status: 'In Progress',
    Actions: { class: 'text-center' },
  };
  todos.value.push(newTodo);
  SaveOnLocalStorage()
}
const modal = useModal()

function openEditModal(id: number) {
  modal.open(EditModal, {
    onSuccess(value) {
      EditTitleById(id, value)
      modal.close()
    }
  })
}
function changeStatusToSuccess(id: number) {
  const todoIndex = todos.value.findIndex(item => item.id === id);
  if (todos.value[todoIndex].Status == 'Completed') {
    todos.value[todoIndex].Status = 'In Progress';
  }
  else {
    todos.value[todoIndex].Status = 'Completed';
  }
  SaveOnLocalStorage()
}
</script>
<template>
  <UModals />
  <header class="Top">
    <div class="flex flex-row justify-center items-center p-5 space-x-2 text-3xl">
      <UIcon name="i-heroicons-calendar-days-16-solid" />
      <h1>Daily Todo-List</h1>
    </div>
  </header>
  <section class="container mx-auto px-5 sm:px-10 md:px-24 lg:px-52 xl:px-72 ">
    <form class="flex justify-center" v-on:submit.prevent>
      <UButtonGroup>
        <UInput v-on:keyup.enter="AddToArray()" size="xl" color="gray" variant="outline" placeholder=""
          v-model="newTodoTitle">
        </UInput>
        <UButton size="xl" @click="AddToArray()">Create</UButton>
      </UButtonGroup>
    </form>
    <UDivider size="sm" class="my-10"></UDivider>
    <UTable :columns="columns" :rows="todos"
      :empty-state="{ icon: 'i-heroicons-circle-stack-20-solid', label: 'No items.' }">
      <template #Actions-data="{ row }">
        <UButton color="gray" variant="ghost" icon="i-heroicons-trash-16-solid" @click="RemoveFromArrayById(row.id)" />
        <UButton color="gray" variant="ghost" icon="i-heroicons-pencil-solid" @click="openEditModal(row.id)" />
        <UButton color="gray" variant="ghost" icon="i-heroicons-check-circle-16-solid"
          @click="changeStatusToSuccess(row.id)" />
      </template>
    </UTable>
  </section>
</template>
<style>
body {
  font-family: 'Roboto', sans-serif;
}
</style>
