<template>
  <section class="container">
     <ul>
       <p>sss</p>
       <li v-for="post in todos" :key="post.id">{{post.id}}:{{post.title}}、{{post.content}}</li>
     </ul>
  </section>
</template>

<script>
import { Component, Vue } from 'vue-property-decorator'
import axios from 'axios'

@Component({})
export default class TodoApp extends Vue{
 todos = []

async mounted() {
  let res = await this.$axios.$get('http://localhost:1337/todos/',{
        identifier: 'tkkun2551@gmail.com',
        password: 'Rock2551'
      })
  console.log(res)
}
}
</script>