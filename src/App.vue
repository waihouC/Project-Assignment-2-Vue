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
            <a class="nav-link active" href="#" v-if="userLoggedIn" @click="goCreate">Create Groupbuy</a>
          </div>
          <form class="d-flex">
            <button class="btn btn-outline-light me-2 me-lg-0 mb-2 mb-lg-0" type="button" 
              v-if="userLoggedIn" @click="logOut">
              Log Out
            </button>
            <button class="btn btn-outline-light me-2 me-lg-0 mb-2 mb-lg-0" type="button" 
              v-else @click="logIn">
              Log In
            </button>
          </form>
        </div>
      </div>
    </nav>
    <main>
      <div class="container-lg" id="page-container">
        <Groupbuy
          :key="updateKey"
          :userLoggedIn="userLoggedIn"
          v-if="page == 'home'" 
          v-on:edit-group="goEdit"
          v-on:force-rerender="forceUpdate"
        />
        <CreateGroup 
          :groupId="editGroupId"
          v-if="page == 'create'"
          v-on:go-to-home="goHome"
          v-on:go-to-error="goError"
        />
        <ErrorPage 
          v-if="page == 'error'"
          v-on:go-to-home="goHome"
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
import ErrorPage from './components/ErrorPage';
export default {
  name: "App",
  components: {
    Groupbuy,
    CreateGroup,
    ErrorPage
  },
  data: function() {
    return {
      page: "home",
      userLoggedIn: false,
      editGroupId: "",
      updateKey: 0
    }
  },
  methods: {
    goHome: function() {
      this.page = "home";
      this.editGroupId = "";
    },
    goCreate: function() {
      this.page = "create";
      this.editGroupId = "";
    },
    goEdit: function(id) {
      this.page = "create";
      this.editGroupId = id;
    },
    goError: function() {
      this.page = "error";
    },
    logIn: function() {
      this.userLoggedIn = true;
    },
    logOut: function() {
      this.userLoggedIn = false;
    },
    forceUpdate: function() {
      // this.$forceUpdate();
      this.updateKey += 1;
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
