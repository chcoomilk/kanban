<template>
  <div>
    <nav class="navbar navbar-dark bg-dark justify-content-between">
      <a class="navbar-brand text-white">Kanban</a>
      <div v-if="page === 'home'">
        <button class="btn btn-outline-danger my-2 my-sm-0" @click="logout()">Logout</button>
      </div>
      <div v-else></div>
    </nav>
    <div v-if="page === 'login'">
      <Login @changePage="changePage"></Login>
    </div>
    <div v-else-if="page === 'register'">
      <Register @changePage="changePage"></Register>
    </div>
    <div v-else-if="page === 'home'">
      <div class="row mx-1 mt-3">
        <CategoryCard
          v-for="category in categories"
          :key="category"
          :category="category"
          @changePage="changePage"
          :tasks="tasks"
          @fetchData="fetchData"
        ></CategoryCard>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "../src/api/axios";
import CategoryCard from "./components/CatergoryCard.vue";
import Login from "./components/Login.vue";
import Register from "./components/Register.vue";

export default {
  name: "App",
  components: {
    CategoryCard,
    Login,
    Register,
  },
  data() {
    return {
      tasks: [],
      categories: ["Back-Log", "To-do", "Doing", "Done"],
      page: "login",
    };
  },
  created: function () {
    if (localStorage.access_token) {
      this.changePage("home");
      this.fetchData();
    } else {
      this.changePage("login");
    }
  },
  computed: {
    filteredTasks() {
      console.log(completedTasks);
    },
  },
  methods: {
    fetchData() {
      axios
        .get("/", {
          headers: {
            access_token: localStorage.access_token,
          },
        })
        .then((res) => {
          const data = res.data;
          this.tasks = data;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    changePage(val) {
      if (val === 'home') {
        this.fetchData();
      }
      
      this.page = val;
    },
    logout() {
      this.page = "login";
      localStorage.removeItem("access_token");
      localStorage.removeItem("username");
      var auth2 = gapi.auth2.getAuthInstance();
      auth2.signOut().then(function () {
      console.log('User signed out.');
    });

    },
  },
};
</script>

<style>
</style>