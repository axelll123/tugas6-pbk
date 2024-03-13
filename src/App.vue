<template>
  <div>
    <form @submit.prevent="save">
      <input type="text" v-model="form.title"> <br>
      <textarea v-model="form.content"></textarea> <br>
      <button type="submit">Save</button>  
    </form>
    <ul>
      <li v-for="article in articles" :key="article.id">
        {{ article.title }} <br>
        {{ article.content }}
        <button @click="edit(article)">Edit</button>
      </li>
    </ul>
    <button @click="load">Load</button>
  </div>
</template>

<script>
import axios from 'axios'

export default{
  data(){
    return{
      form: {
        id: null, // tambahkan id untuk menyimpan id artikel yang diedit
        title:'',
        content: ''
      },
      articles: []
    }
  },

  async mounted(){
    this.load() // panggil metode load saat komponen dimuat
  },

  methods: {
    async load() {
      const response = await axios.get('http://localhost:3000/articles')
      this.articles = response.data
    },

    async save() {
      try{
        if (this.form.id) { // Jika form.id ada, berarti sedang mengedit artikel
          await axios.put(`http://localhost:3000/articles/${this.form.id}`, this.form)
        } else { // Jika tidak, berarti sedang menambahkan artikel baru
          await axios.post('http://localhost:3000/articles', this.form)
        }
        this.load()
        this.form.title = ''
        this.form.content = ''
        this.form.id = null // Reset form.id setelah menyimpan
      } catch (e) {
        console.log(e)
      }
    },

    async edit(article) {
      // Mengisi form dengan data artikel yang akan diedit
      this.form.id = article.id
      this.form.title = article.title
      this.form.content = article.content
    }
  }
}
</script>
