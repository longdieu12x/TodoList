<template>
    <header>
        <h1>Dang's Todo List</h1>
    </header>
    <form v-on:submit.prevent="addTodo()">
        <input type="text" class="input-content" v-model="dataInput">
        <button type="submit" class="button-content">
            <i class="fas fa-plus-circle"></i>
        </button>
        <div class="select">
            <select v-model="selected">
                <option value="All">All</option>
                <option value="Completed">Completed</option>
                <option value="Uncompleted">Uncompleted</option>
            </select>
        </div>
    </form>
    <div class="container">
        <ul class="list">
              <!-- changed the key to use the index instead, because we don't have an id -->
            <div v-for="item in filteredListData" :key="item.index" :class="{ completed: item.isCompleted, fail: item.isFailed}" class="todo">
                <li class="todo-item" :class="{ completed: item.isCompleted, fail: item.isFailed}">{{item.name}}</li>
                <button class="complete-btn" @click="completeTodo(item)"><i class="fas fa-check" :class="{ completed: item.isCompleted}"></i></button>
                <button class="trash-btn" @click="removeTodo(item)"><i class="fas fa-trash"></i></button>
            </div>
        </ul>
    </div>
      <!-- added these so you can easily see how the data changes -->
      <pre>
        listData: {{listData}}
    </pre>
      <pre>
        filteredListData: {{listData}}
    </pre>
</template>
<script>
import {reactive, ref, toRefs, onMounted, watch, computed } from 'vue'
export default {
    setup(){

        //Declare State
        const selected = ref('All')
        let listData = ref([
            {
                name: 'Do homework',
                isCompleted: false,
                isFailed: false,
            },
        ])
        const filteredListData = computed(() => {
            // indexedListData adds the index property to the todos.
            // this is important so we can find the right todo to complete/delete
            // even when the todos are filtered. This is because the index within listData
            // and filteredListData may be diferent.
            // For example if the first item is completed, and we are only showing completed items,
            // the item at index 0 in filteredListData will not be the same as the item at index 0 in listData
            // an alternative would be adding a proper, unique ID to each todo at creation.
            const indexedListData = listData.value.map((todo, index) => ({ ...todo, index }))
            console.log(indexedListData)
            if (selected.value === 'All') {
                return indexedListData
            } else if (selected.value === 'Completed') {
                return indexedListData.filter(todo => todo.isCompleted)
            } else if (selected.value === 'Uncompleted') {
                return indexedListData.filter(todo => !todo.isCompleted)
            } else {
                throw new Error(`Unknow selected value: ${selected.value}`)
            }
        })
        let dataInput = ref('')
        
        //Declare Functions
        function addTodo(){
            listData.value.push({
                name: dataInput.value,
                isCompleted: false,
                ssFailed: false,
            })
            dataInput.value = ''
        }
      
        function removeTodo(todo){
              // read up on array.splice(): https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice

              listData.value.splice(todo.index, 1) // delete starting at todo.index, 1 item
        }
        function completeTodo(todo){
              listData.value[todo.index].isCompleted = true
        }
      
          // notes: you are currently not doing anything with todo.isFailed. the items are deleted straight away.
        return {
            listData,
            filteredListData,
            dataInput,
            addTodo,
            removeTodo,
            completeTodo,
            selected,
        }
    }
}
</script>

<style>

</style>