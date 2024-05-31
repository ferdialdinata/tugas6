<template>
  <div class="background">
    <img src="https://images.pexels.com/photos/2150/sky-space-dark-galaxy.jpg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1"/>
  </div>
  <div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">POST</h1>
    <div class="mb-4 ga">
      <input v-model="title" type="text" placeholder="Judul" class="border p-2 mr-2"/>
      <input v-model="content" type="text" placeholder="Isi Konten" class="border p-2 mr-2"/>
      <button @click="savePost" class="bg-blue-500 text-white p-2">Simpan</button>
    </div>
    <div v-for="post in posts" :key="post.id" class="border border1 p-4 mb-4">
      <h2 class="text-xl font-bold">{{ post.title }}</h2>
      <p>{{ post.content }}</p>
      <button @click="editPost(post)" class="bg-yellow-500 text-white p-2 mr-2">Edit</button>
      <button @click="deletePost(post.id)" class="bg-red-500 text-white p-2">Delete</button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const title = ref('');
const content = ref('');
const posts = ref([]);
const editMode = ref(false);
const editId = ref(null);

const fetchPosts = async () => {
  try {
    const response = await axios.get('http://localhost:3000/posts');
    posts.value = response.data;
  } catch (error) {
    console.error("There was an error fetching the posts!", error);
  }
};

const savePost = async () => {
  if (editMode.value) {
    await axios.put(`http://localhost:3000/posts/${editId.value}`, {
      title: title.value,
      content: content.value
    });
  } else {
    await axios.post('http://localhost:3000/posts', {
      title: title.value,
      content: content.value
    });
  }
  fetchPosts();
  resetForm();
};

const editPost = (post) => {
  editMode.value = true;
  editId.value = post.id;
  title.value = post.title;
  content.value = post.content;
};

const deletePost = async (id) => {
  await axios.delete(`http://localhost:3000/posts/${id}`);
  fetchPosts();
};

const resetForm = () => {
  title.value = '';
  content.value = '';
  editMode.value = false;
  editId.value = null;
};

onMounted(fetchPosts);
</script>

<style scoped>
.container {
  max-width: 600px;
  margin-top: 30px;
}

.ga input{
  display: flex;
  margin-bottom: 10px;
}

h1{
  color: white;
}

.border1{
  backdrop-filter: blur(5px);
  color: white;
}

.background img{
  position: fixed;
  top: 0;
  left: 0;
  min-height: 90%;
  min-width: 1500px;
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: -2;
  object-fit: cover;
  -webkit-background-size:cover; -moz-background-size:cover; -o-background-size:cover; background-size: cover;
  filter: brightness(0.8);
}
</style>
