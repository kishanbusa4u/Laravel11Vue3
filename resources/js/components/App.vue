<template>
    <div class="container mt-5">
      <h1 class="mb-4">CRUD Posts</h1>
  
      <!-- Form to Create or Edit Posts -->
      <div class="card mb-4">
        <div class="card-body">
          <h5 class="card-title">{{ isEdit ? 'Edit Post' : 'Create Post' }}</h5>
          <div class="mb-3">
            <label class="form-label">Title</label>
            <input
              type="text"
              v-model="form.title"
              class="form-control"
              :class="{ 'is-invalid': errors.title }"
              placeholder="Enter title"
            />
            <div v-if="errors.title" class="invalid-feedback">{{ errors.title }}</div>
          </div>
          <div class="mb-3">
            <label class="form-label">Content</label>
            <textarea
              v-model="form.content"
              class="form-control"
              :class="{ 'is-invalid': errors.content }"
              placeholder="Enter content"
            ></textarea>
            <div v-if="errors.content" class="invalid-feedback">{{ errors.content }}</div>
          </div>
          <button @click="submitPost" class="btn btn-primary">{{ isEdit ? 'Update' : 'Create' }}</button>
        </div>
      </div>
  
      <!-- Posts List -->
      <div v-if="posts.length" class="list-group">
        <div v-for="post in posts" :key="post.id" class="list-group-item list-group-item-action d-flex justify-content-between align-items-start">
          <div class="ms-2 me-auto">
            <h5 class="mb-1">{{ post.title }}</h5>
            <p class="mb-1">{{ post.content }}</p>
          </div>
          <button class="btn btn-secondary btn-sm me-2" @click="editPost(post)">Edit</button>
          <button class="btn btn-danger btn-sm" @click="deletePost(post.id)">Delete</button>
        </div>
      </div>
  
      <div v-else class="alert alert-warning mt-4">No posts found. Please create a post.</div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        posts: [],
        form: {
          title: '',
          content: '',
        },
        isEdit: false,
        editId: null,
        errors: {},
      };
    },
    mounted() {
      this.getPosts();
    },
    methods: {
      getPosts() {
        axios.get('/api/posts')
          .then(response => {
            this.posts = response.data;
          });
      },
      validateForm() {
        this.errors = {};
  
        if (!this.form.title) {
          this.errors.title = 'The title field is required.';
        }
  
        if (!this.form.content) {
          this.errors.content = 'The content field is required.';
        }
  
        return Object.keys(this.errors).length === 0;
      },
      submitPost() {
        if (this.validateForm()) {
          if (this.isEdit) {
            axios.put(`/api/posts/${this.editId}`, this.form)
              .then(() => {
                this.getPosts();
                this.resetForm();
              });
          } else {
            axios.post('/api/posts', this.form)
              .then(() => {
                this.getPosts();
                this.resetForm();
              });
          }
        }
      },
      editPost(post) {
        this.isEdit = true;
        this.editId = post.id;
        this.form.title = post.title;
        this.form.content = post.content;
      },
      deletePost(id) {
        axios.delete(`/api/posts/${id}`)
          .then(() => {
            this.getPosts();
          });
      },
      resetForm() {
        this.form.title = '';
        this.form.content = '';
        this.isEdit = false;
        this.editId = null;
        this.errors = {};
      },
    },
  };
  </script>
  
  <style scoped>
  .container {
    max-width: 800px;
  }
  </style>
  