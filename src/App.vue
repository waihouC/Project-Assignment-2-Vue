<template>
  <div class="container-fluid p-0">
    <nav class="navbar navbar-expand-lg navbar-dark bg-secondary sticky-top">
      <div class="container-fluid container-lg">
        <span class="navbar-brand my-auto mx-4 ms-lg-0 fs-2">Tompang.sg</span>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent"
        aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <div class="navbar-nav me-auto mb-2 mb-lg-0">
            <a class="nav-link active" aria-current="page" href="#" @click="goHome">Home</a>
            <a class="nav-link" href="#" @click="goCreate">Create Groupbuy</a>
          </div>
          <form class="d-flex">
            <button class="btn btn-outline-light me-2 me-lg-0 mb-2 mb-lg-0" type="button">Log In</button>
          </form>
        </div>
      </div>
    </nav>
    <main>
      <div class="container-lg" id="page-container">
        <Groupbuy v-if="page == 'home'" />
        <CreateGroup 
          v-if="page == 'create'"
          v-on:cancel-create="goHome"
          v-on:new-group-created="goHome" 
        />
      </div>
    </main>
    <footer>
      <div class="text-center p-3 bg-dark text-white">
        &copy; 2021 Tompang.sg | Creator: waihouC
      </div>
    </footer>
  </div>
</template>

<script>
import Groupbuy from './components/Groupbuy';
import CreateGroup from './components/CreateGroup';
export default {
  name: "App",
  components: {
    Groupbuy,
    CreateGroup
  },
  data: function() {
    return {
      page: "home",
    }
  },
  methods: {
    goHome: function() {
      this.page = "home";
    },
    goCreate: function() {
      this.page = "create";
    }
  }
};
</script>

<style>
#page-container {
  min-height: calc(100vh - 130px);
}

.navbar-brand {
  font-family: "MV Boli", cursive;
}

.nav-link {
  font-size: 1.25rem;
}

.nav-link:hover {
  text-decoration: underline;
}

.navbar-collapse {
  display: flex;
}
</style>
