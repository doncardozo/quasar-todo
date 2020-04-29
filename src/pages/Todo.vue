<template>
  <q-page class="bg-grey-3 column">

    <div class="row q-pa-sm bg-primary">
      <q-input
        v-model="newTask"
        @keyup.enter="addTask"
        class="col"
        square
        filled
        bg-color="white"
        placeholder="Add Task"
        dense
      >
      <template v-slot:append>
        <q-btn round dense flat icon="add" @click="addTask" />
      </template>
      </q-input>
    </div>

    <q-list 
      class="bg-white"
      separator
      bordered
      >      
      <q-item 
        tag="label" 
        v-ripple
        v-for="(task, index) in tasksList"
        :key="index"
        @click="task.done = !task.done"
        clickable
        :class="{ 'done': task.done }"
        >
        <q-item-section avatar>
          <q-checkbox v-model="task.done" color="primary" />
        </q-item-section>
        <q-item-section>
          <q-item-label>{{ task.title }}</q-item-label>
        </q-item-section>
        <q-item-section
          v-if="task.done"
          side>
          <q-btn 
            flat 
            round
            dense 
            color="primary" 
            icon="delete"
            @click="deleteTask(index,task.id)" />
        </q-item-section>
      </q-item>

    </q-list>

    <div v-if="!this.tasks.length" class="no-tasks absolute-center">
      <q-icon
        name="check"
        size="100px"
        color="primary"
      />
      <div class="text-5 text-primary text-center">
        No tasks
      </div>
    </div>

  </q-page>
</template>

<script>

import { db } from 'boot/firebase'

export default {
  name: "PageIndex",
  data(){
    return {
      newTask: '',
      tasks: []
    }
  },
  computed: {
    tasksList(){
      return this.tasks
    }
  },
  created(){
    this.getTask()
  },
  methods: {
    async getTask(){
      try {
        this.$q.loading.show()
        const tasks = await db.collection('tasks').get()
        tasks.forEach(el => {
          let task = {id: el.id, title: el.data().title, done: el.data().done}
          this.tasks.push(task)
        })
      } catch (error) {
        // this.$q.notify('No data found')
        console.log(error)
      } finally {
        this.$q.loading.hide()
      }
    },
    async addTask(){      
        if(this.newTask.length <= 0){
          this.$q.notify('No data to insert')
          return
        }
        this.$q.loading.show()
        await db.collection('tasks').add({          
          title: this.newTask,
          done: false
        })
        .then((response) => {          
          this.tasks.push({
            id: response.id,
            title: this.newTask,
            done: false
          })
          this.newTask = ''
          this.$q.notify('Task was successfully added')      
          this.$q.loading.hide() 
        })
        .catch(err => console.log(err))
    },
    deleteTask(index, id){      
       this.$q.dialog({
        title: 'Confirm',
        message: `Would you like to delete that?`,
        cancel: true,
        persistent: true
      }).onOk(async () => {
        try {
          await db.collection('tasks').doc(id).delete()        
          this.tasks.splice(index, 1)
          this.$q.notify('Task was successfully deleted')          
        } catch (error) {
          console.log(error)
        }
      })    
    }
  }
};
</script>

<style lang="scss">
  .done {
    .q-item__label {
      text-decoration: line-through;
      color: #bbb;
    }
  }
</style>