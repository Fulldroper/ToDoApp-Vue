<template>
  <div id="main">
    <Input @add="add" />
    <div class="container" >
      <div
        v-for="task in taskList"
        :data-id="task._id" 
        :class='task.isDone ? "inactive input" : "active input"' 
        :value="task.value" 
        :disabled="task.isNotEditing"
      >
        <div :data-id="task._id"
          @click = "toggleStatus"
          @dblclick = "enableEdit"
        >
        {{task.value}}
        </div>
        <input style="display:none;" @keyup.enter = "disableEdit" :data-id="task._id" :value="task.value" />
        <img :data-id="task._id" class="btn-img" @click="del"   src="del.svg" />
      </div>
      Completed {{completed}} out of {{tasks}} tasks.
    </div>    
  </div>
</template>


<script>
  import Input from '@/components/Input.vue'  

  import axios from "axios";
  export default {
    components: {
      Input
    },
    data() {
      return {
        taskList: [],
        completed: 0,
        tasks: 0,
        timeout: 600
      }
    },
    mounted() {
      this.getList()
      setInterval(this.getList,60000)
    },
    methods: {
      getList() {
        axios.get("http://localhost:8080/")
        .then(response => {
          const finished = []
          const unfinished = []
          response.data.forEach(el => {
            if(el.isDone) finished.push(el)
            else unfinished.push(el)
          })
          finished.sort()
          unfinished.sort()
          this.taskList = [...unfinished.reverse(),...finished.reverse()];
          this.completed = finished.length
          this.tasks = response.data.length
        })
        .catch(error => {
          console.log(error)
        })
      },
      add(input) {
        axios.put("http://localhost:8080/", {value: input})
        setTimeout(this.getList, this.timeout)
      },
      del(event) {        
        axios.delete(`http://localhost:8080/${event.target.dataset.id}/`)
        setTimeout(this.getList, this.timeout)
      },
      toggleStatus(input) {
        const task = JSON.parse(JSON.stringify(this.taskList)).find(el => el._id === event.target.dataset.id)
        const status = task.isDone ? "inactive" : "active"
        axios.patch(`http://localhost:8080/${status}/${task._id}/`)
        setTimeout(this.getList, this.timeout)
      },
      enableEdit(event) {
        event.target.style.display = "none";
        event.target.parentElement.querySelector('input').style.display = "block"
      },
      disableEdit(event) {
        event.target.style.display = "none";
        event.target.parentElement.querySelector('div').style.display = "block"
        axios.patch(`http://localhost:8080/${event.target.dataset.id}/`, {value: event.target.value})
        setTimeout(this.getList, this.timeout)
      },
    }
  }
</script>
<style>
.container, #app {
  display: flex;
  flex-direction: column;
}
#app {
  margin-top: 50px;
  align-items: center;
  justify-content: center;
}
#main {
  max-width:650px;
  width: 100%;
}
.input {
  margin-bottom: 5px;
  padding: 5px;
  display: flex;
  justify-content: space-between;
  flex-direction: row;
  cursor: pointer;
}
.input > h3 {
  width: max-content;
}
.input:hover > .btn-img {
  opacity: 1;
}
.btn-img {
  width: 25px;
  padding: 5px;
  cursor: pointer;
  opacity: 0;
}
.inactive {
  opacity: 0.5;
  text-decoration: line-through;
}
</style>