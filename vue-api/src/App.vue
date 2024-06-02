<template>
  <div class="app-container">
    <form @submit.prevent="save" class="form-container">
      <input type="text" v-model="form.title" placeholder="Title" /><br />
      <textarea v-model="form.content" placeholder="Content"></textarea><br />
      <button type="submit">Save</button>
    </form>
    <ul class="articles-list">
      <li v-for="article in articles" :key="article.id">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <button @click="edit(article)">Edit</button>
      </li>
    </ul>
    <button @click="load">Load</button>
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
      content: ''
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
        // Assuming successful save, update UI and reset form
        if (method === 'post') {
          articles.value.push(response.data);
        } else {
          articles.value = articles.value.map(article =>
            article.id === response.data.id ? response.data : article
          );
        }
        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, load };
  }
};
</script>

<style scoped>
.app-container {
  padding: 20px;
  max-width: 600px;
  margin: auto;
  background-color: #222;
  color: #fff;
}

.form-container {
  margin-bottom: 20px;
}

.form-container input,
.form-container textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: none;
  border-radius: 5px;
}

.form-container button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #4CAF50;
  color: white;
  cursor: pointer;
}

.form-container button:hover {
  background-color: #45a049;
}

.articles-list {
  list-style-type: none;
  padding: 0;
}

.articles-list li {
  background: #333;
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 5px;
}

.articles-list h3 {
  margin: 0;
}

.articles-list p {
  margin: 10px 0;
}

.articles-list button {
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  background-color: #2196F3;
  color: white;
  cursor: pointer;
}

.articles-list button:hover {
  background-color: #0b7dda;
}

button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #4CAF50;
  color: white;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}
</style>
