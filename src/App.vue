<script setup>
import { ref, onMounted, computed, watch} from 'vue'

const todo = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

//오름차순 반환 저장 , 날짜 목록으로 최신화
const todos_asc = computed(()=> todo.value.sort((a, b)=>{
  return b.createdAt - a.createdAt
}))
const addTodo = ()=>{

}

//이름이 변경될 시 변경된 새 값을 얻고 로컬스토리지 이름으로 설정
watch(name,(newVal)=> {
  localStorage.setItem('name', newVal)
})


//마운트로 새로고침 시 이름이 동일하도록 설정
onMounted(()=>{
  name.value = localStorage.getItem('name') || ''
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
            <!-- category 라디오 버튼으로 만들기 1레이블로, business -->
            <input type="radio" name="category" value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        
      </form>
    </section>

  </main>

</template>