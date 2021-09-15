<template>
  <div class="container-fluid p-0">
    <nav class="navbar navbar-expand-lg navbar-dark bg-secondary">
      <div class="container-fluid">
        <a class="navbar-brand ms-2 fs-2" href="#" >Groupee &trade;</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent"
        aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <div class="navbar-nav me-auto mb-2 mb-lg-0">
            <a class="nav-link active" aria-current="page" href="#">Home</a>
            <a class="nav-link" href="#">Create Groupbuy</a>
          </div>
          <form class="d-flex">
            <button class="btn btn-outline-light me-2 mb-2 mb-lg-0" type="button">Log In</button>
          </form>
        </div>
      </div>
    </nav>
    <main>

    </main>
    <footer>
      <div class="text-center p-3 bg-dark text-white">
        &copy; 2021 Groupee | Creator: waihouC
      </div>
    </footer>
  </div>
</template>

<script>
export default {
  name: "App",
};
</script>

<style>
nav {
  position: sticky !important;
  top: 0px;
}

.navbar-brand {
  font-family: "Bradley Hand", cursive;
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

main {
  height: 100vh;
}
</style>
