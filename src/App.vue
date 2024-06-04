<template>
  <div class="container">
    <form @submit.prevent="save" class="form">
      <input type="text" v-model="form.title" placeholder="Title" /><br />
      <textarea v-model="form.content" placeholder="Content"></textarea><br />
      <button type="submit">{{ form.id ? 'Update' : 'Save' }}</button>
    </form>
    <ul class="articles">
      <li v-for="article in articles" :key="article.id" class="article">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <button @click="edit(article)">Edit</button>
        <button @click="remove(article.id)">Delete</button>
      </li>
    </ul>
    <button @click="load" class="load-button">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: '',
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';
        const response = await axios[method](url, form);

        if (form.id) {
          const index = articles.value.findIndex(article => article.id === form.id);
          if (index !== -1) {
            articles.value[index] = response.data;
          }
        } else {
          articles.value.push(response.data);
        }

        resetForm();
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function remove(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    function resetForm() {
      form.id = null;
      form.title = '';
      form.content = '';
    }

    onMounted(load);

    return { form, articles, save, edit, remove, load };
  },
};
</script>

<style>
body {
  background-color: #121212;
  color: #33ff33;
  font-family: Arial, sans-serif;
}

.container {
  max-width: 600px;
  margin: auto;
  padding: 1rem;
  border: 1px solid #33ff33;
  border-radius: 8px;
  background-color: #1e1e1e;
}

.form {
  margin-bottom: 1rem;
}

.form input,
.form textarea {
  width: 100%;
  margin-bottom: 0.5rem;
  padding: 0.5rem;
  border: 1px solid #33ff33;
  border-radius: 4px;
  background-color: #333;
  color: #33ff33;
}

.form button {
  background-color: #33ff33;
  color: #1e1e1e;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.form button:hover {
  background-color: #28cc28;
}

.articles {
  list-style-type: none;
  padding: 0;
}

.article {
  background-color: #2a2a2a;
  padding: 1rem;
  border: 1px solid #33ff33;
  border-radius: 8px;
  margin-bottom: 1rem;
}

.article h3 {
  margin-top: 0;
}

.article button {
  background-color: #33ff33;
  color: #1e1e1e;
  border: none;
  padding: 0.3rem 0.6rem;
  border-radius: 4px;
  cursor: pointer;
  margin-right: 0.5rem;
  transition: background-color 0.3s;
}

.article button:hover {
  background-color: #28cc28;
}

.article button:last-child {
  background-color: #ff3333;
}

.article button:last-child:hover {
  background-color: #cc2828;
}

.load-button {
  background-color: #33ff33;
  color: #1e1e1e;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.load-button:hover {
  background-color: #28cc28;
}
</style>
