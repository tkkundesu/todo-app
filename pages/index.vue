<template>
   <div class="my-todo-list">
     <div class="">
       <span class="add-icon" @click="createButtonHandler">➕</span>
     </div>
     <div class="todos-list">
       <todos-panel primary title="未実行" class="notCompatibleTodos">
          <draggable group="todos" :list="notCompatibleTodos" @change="notCompatibleTodosAddHandler">
            <todos-panel v-for="(todo, index) in notCompatibleTodos" :key="index" class="task-item">
                <div>
                   <p>{{ todo.title }}　<span @click="editButtonHandler(todo)" class="icon">🖋</span><span @click="deleteButtonHandler(todo)" class="icon">✖︎</span></p>
                </div>
                <div class="deadline">
                    <span v-if="todo.deadline_at">期限：{{ datetimeFormat(todo.deadline_at) }}</span>
                </div>
            </todos-panel>
          </draggable>
        </todos-panel>
        <todos-panel danger title="実行中" class="processingTodos">
          <draggable group="todos" :list="processingTodos" @change="processingTodosAddHandler">
            <todos-panel v-for="(todo, index) in processingTodos" :key="index" class="task-item">
                <div>
                   <p>{{ todo.title }} <span @click="editButtonHandler(todo)" class="icon">🖋</span><span @click="deleteButtonHandler(todo)" class="icon">✖︎</span></p>
                </div>
                <div class="deadline">
                    <span v-if="todo.deadline_at">期限：{{ datetimeFormat(todo.deadline_at) }}</span>
                </div>
            </todos-panel>
          </draggable>
        </todos-panel>
        <todos-panel success title="実施済" class="processedTodos">
          <draggable group="todos" :list="processedTodos" @change="processedTodosAddHandler">
            <todos-panel v-for="(todo, index) in processedTodos" :key="index" class="task-item">
                <div>
                   <p>{{ todo.title }}  <span @click="editButtonHandler(todo)" class="icon">🖋</span><span @click="deleteButtonHandler(todo)" class="icon">✖︎</span></p>
                </div>
                <div class="deadline">
                    <span v-if="todo.deadline_at">期限：{{ datetimeFormat(todo.deadline_at) }}</span>
                </div>
            </todos-panel>
          </draggable>
        </todos-panel>
    </div>
    <todo-edit
      v-if="editModalVisible"
      :visible.sync="editModalVisible"
      :model.sync="editModalModel"
      @todo-edit-finished="editFinished"
    />
  </div>
</template>

<script lang="ts">
import _ from 'lodash'
import axios from 'axios'
import { Component, Vue } from 'vue-property-decorator'
import draggable from 'vuedraggable'
import moment from 'moment'
import TodosPanel from '~/components/TodosPanel.vue'
import TodoEdit from '~/components/TodoEdit.vue'


@Component({
  components: { draggable,　TodosPanel, TodoEdit, }
  })
export default class TodoApp extends Vue {
  todos: Array<any> = []
  notCompatibleTodos: Array<any> = []
  processingTodos: Array<any> = []
  processedTodos: Array<any> = []
  editModalModel: any = {}
  editModalVisible: boolean = false

  async loadTodos() {
      const dd = await this.$axios.$get('http://localhost:1337/todos/',{
        identifier: 'tkkun2551@gmail.com',
        password: 'tkkundesu'
      })
      this.todos = dd
      console.log(dd)
      this.notCompatibleTodos = this.todos.filter((data) => {
          return data.status === 0
      })
      this.processingTodos = this.todos.filter((data) => {
          return data.status === 1
      })
      this.processedTodos = this.todos.filter((data) => {
          return data.status === 2
      })
  }

  mounted() {
    this.loadTodos()
  }

  notCompatibleTodosAddHandler(data: any) {
    if (data.added) {
      const params = data.added.element
      params.status = 0
      this.$axios.$put('http://localhost:1337/todos/' + params.id, params,{
        identifier: 'tkkun2551@gmail.com',
        password: 'tkkundesu'
      }).catch((err: any) => {
          alert(err)
      })
    }
  }
  processingTodosAddHandler(data: any) {
    if (data.added) {
      const params = data.added.element
      params.status = 1
      this.$axios.$put('http://localhost:1337/todos/' + params.id, params,{
        identifier: 'tkkun2551@gmail.com',
        password: 'tkkundesu'
      }).catch((err: any) => {
          alert(err)
      })
    }
  }
  processedTodosAddHandler(data: any) {
    if (data.added) {
      const params = data.added.element
      params.status = 2
      this.$axios.$put('http://localhost:1337/todos/' + params.id, params,{
        identifier: 'tkkun2551@gmail.com',
        password: 'tkkundesu'
      }).catch((err: any) => {
          alert(err)
      })
    }
  }
  datetimeFormat(data: any) {
    if (data) {
        return moment(data).format('YYYY年M月D日 HH:mm')
    }
    return null
  }
    // 追加ボタンハンドラ
  createButtonHandler() {
      this.editModalModel = {
        title: null,
        content: null,
        deadline_at: null,
        status: 0,
        deleted: 0,
      }
      this.editModalVisible = true
  }
  // 修正ボタンハンドラ
  editButtonHandler(todo: any) {
      this.editModalModel = _.cloneDeep(todo)
      this.editModalVisible = true
  }
  editFinished() {
      this.loadTodos()
  }
   async deleteButtonHandler(todo: any) {
    await this.$axios.$delete('http://localhost:1337/todos/' + todo.id,{
        identifier: 'tkkun2551@gmail.com',
        password: 'tkkundesu'
      }).catch((e: any) => {
      alert(e)
    })
    this.loadTodos()
  }
}
</script>

<style lang="stylus">
.todos-list {
    display: flex;
    max-width: 1470px;
    text-align: center;
}
.notCompatibleTodos {
  width: 30%;
  margin 5px 20px
}
.processingTodos {
  width: 30%;
  margin 5px 20px
}
.processedTodos {
  width: 30%;
  margin 5px 20px
}
.task-item {
    font-size:18px;
    margin:50px auto 50px auto;
    width: 350px;
    border-radius: 20px;
}
.deadline {
  font-size: 8px;
}
.add-icon
  font-size 35px
  margin 30px
  cursor pointer
.icon
  font-size 15px
  cursor pointer
</style>
