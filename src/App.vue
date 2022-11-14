<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref('')



const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}
))

const addTodo = () => { 
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  todos.value.push ({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date.getTime()
  })
  
}

//Sparar namnen i localstorage
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
})




</script>



<template>

  <main class="app">

    <section class="greeting">
      <h2 class="title">
        what's up, <input type="text" placeholder="Name here" v-model="name" />


      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="e.g. make a video" v-model="input_content" />


        <h4>Pick a category</h4>

        <div class="option">

          <label>
            <input 
            type="radio" 
            name="category" 
            id="category1" 
            value="business" 
            v-model="input_category" />

            <span class="bubble business"></span>
            <div>Business</div>

          </label>

          <label>
            <input 
            type="radio" 
            name="category" 
            id="category1" 
            value="personal" 
            v-model="input_category" />

            <span class="bubble personal"></span>
            <div>personal</div>

          </label>
          </div>

          <input type="submit" value="add todo" />

      </form>
    </section>

  </main>


</template>


