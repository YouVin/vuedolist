<script setup>
import { ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

//오름차순 반환 저장 , 날짜 목록으로 최신화
const todos_asc = computed(()=> todos.value.sort((a, b)=>{
  return b.createdAt - a.createdAt
}))
const addTodo = ()=>{
  //addTodo칸이 공백 일경우 아무것도 return 하지 않는다. 
  if(input_content.value.trim()==='' || input_category.value === null){
    return
  }

  todos.value.push({
    content : input_content.value,
    category : input_category.value,
    done : false,
    createdAt : new Date().getTime()
  })
  input_content.value = ''
  input_category.value = null
}

const removeTodo = todo =>{
  todos.value = todos.value.filter(t => t!==todo)
}

watch(todos, newVal =>{
  //JSON.stringify -> 객체를 JSON 형식으로 바꿔준다.
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep : true })//중첩데이터 watch 하기, 속성 내부를 검사

//이름이 변경될 시 변경된 새 값을 얻고 로컬스토리지 이름으로 설정
watch(name,(newVal)=> {
  localStorage.setItem('name', newVal)
}) 


//마운트로 새로고침 시 이름이 동일하도록 설정
onMounted(()=>{
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>

  <main class="app">
    <section class="greeting">
      <h2 class="title">
        what's up, <input type="text" placeholder="Name here" 
        v-model="name"/>
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create a todo</h3>

      <!-- 화면이 리로드 되면서 데이터가 날라가는것을 방지 -->
      <form @submit.prevent ="addTodo">
        <h4>What's on your todo list?</h4>
        <!-- todolist 들어갈 텍스트창 만들기 -->
        <input type="text" placeholder="e.g. make a video" v-model="input_content" />
        <h4>Pick a category</h4>
        
        <div class="options">
          <label>
            <!-- category 라디오 버튼으로 만들기 1레이블로, business -->
            <input type="radio" name="category" value="business" v-model="input_category"/>
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <!-- category 라디오 버튼으로 만들기 2레이블로, personal -->
            <input type="radio" name="category" value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo">
      </form>
    </section>
      
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <!-- 만든 todo list 배열 표시 -->
      <div class="list">
        <div v-for="todo in todos_asc" :key="todo" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <!-- 체크박스 색깔 정하기 category에 따라서 -->
            <!-- v-model 통해서 클릭 되었을 시 데이터 바인딩-->
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <!-- 삭제 버튼 클릭이벤트 설정 및 함수 적용-->
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
   
  </main>

</template>