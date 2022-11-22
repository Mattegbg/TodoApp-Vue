<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref('')


//Här jämför vi får 'date' i millisekunder från nedan i todos.value.push
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}
))


//Om "input_content.value.trim() === '' *TOM STRÄNG*   " detta gör att vi förhindrar att någon skickar in en tom sträng/string
const addTodo = () => { 
  if (input_content.value.trim() === '' || input_category.value === null) {  
    return
  }

  //Pushar in content i arrayen men ej i localstorage, vilket vi får göra nedan.
  todos.value.push ({
    content: input_content.value,
    category: input_category.value,
    done: false, //detta behöver vi så att det ej sätts som klart som standard/default? skall spara 
    createdAt: new Date().getTime() //retunerar vår 'date' i millisekunder för att jämföra i vår todos_asc (ascending) ovan
  })

  //Detta tömmer textfältet på hemsidan efter vi skrivit in en "todo" och lagt till det i listan.
  input_content.value = ''
  input_category.value = null
  
}

//Delete funktion för delete knappen längst ner så man kan ta bort sina todos på sidan
const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo) //Här loopar vi alla todos, är det INTE "lika med" todo skickar vi tillbaka till arrayen. Om det ÄR lika med todo skickar vi EJ tillbaka till array & det retunerar "FALSE" samt vi får endast de vi vill ha
}


//Watch funkar med det initiala värdet satt ovan men funkar ej med "push" för att push lägger till saker i det? 
//Det enda sättet att kalla på en "watch" (nedan) är att totalt ändra på todos value som tex " todos.value = blabla " eller som exempel nedan (deep) / options
//Sparar todos ovan i localstorage
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true }) // Deep är en "option" som tittar igenom alla individuella array items i todo.value och om något ändras i det fångas det upp och kallar på det i localstorage igen.

//Sparar namnen i localstorage
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})


//Todos arrayen är tom, här hämtar/pull'ar vi fram det
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || [] //här hämtar vi / pull'ar  todos eller array. Samt JSON.parse för vi stringifiade det ovan. || betyder "or/eller"
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

      <!-- Nedan hämtar vi todos (från ovan i script "todos_asc" rad 12) som vi skickar in i arrayen / i inputfältet på sidan -->

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'} `">

          <label>
            <input type="checkbox" v-model="todo.done"/>
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <!-- detta skickar ut texten i todo rutorna på sidorna och gör att man kan uppdatera rutorna med annan text via localstorage -->
          <div class="todo-content">
            <input type="text" v-model="todo.content"> 
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)" >Delete</button> 
          </div>

        </div>
      </div>
    </section>

  </main>


</template>


