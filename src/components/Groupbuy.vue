<template>
  <div>
    <div class="overlay">
      <div class="container-lg">
        <form class="p-4 d-flex">
          <div class="input-group">
            <input type="search" class="form-control rounded-2" placeholder="Search for product..."
              id="search-box" aria-label="Search" aria-describedby="search-button" />
            <div class="input-group-btn">
              <button class="btn btn-secondary btn-sm" type="submit" id="search-button">
                <img src="../assets/img/search-symbol.png" />
              </button>
            </div>
          </div>
        </form>
      </div>
    </div>
    <div class="border border-secondary rounded-3 p-4 pt-2 my-4">
      <div class="mb-3">
        <span class="fw-bold">{{ this.groupbuys.length }}</span> available groups:
      </div>
      <div class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4">
        <div class="col" v-for="g in groupbuys" v-bind:key="g._id">
          <div class="card border-dark h-100">
            <div class="card-header bg-gray400">
              <h4>{{ g.groupName }}</h4>
            </div>
            <div class="card-body">
              <h5 class="card-title">{{ g.userName }}</h5>
              <h6 class="card-subtitle mb-2 text-muted">{{ g.contact }}</h6>
              <p class="card-text">{{ g.description }}</p>
            </div>
            <ul class="list-group list-group-flush">
              <li class="list-group-item">
                <span class="fw-bold">Category:</span> {{ g.category }}
              </li>
              <li class="list-group-item">
                <span class="fw-bold">Price:</span> ${{ parseFloat(g.price).toFixed(2) }}
              </li>
              <li class="list-group-item">
                <span class="fw-bold">Location:</span> {{ g.location }}
              </li>
              <li class="list-group-item">
                <span class="fw-bold">Max Orders:</span> {{ g.maxOrders }}
              </li>
              <li class="list-group-item">
                <span class="fw-bold">Deadline:</span> {{ g.deadline | formatDate }}
              </li>
              <li class="list-group-item" v-bind:class="{'d-none': g.tags.length == 0}">
                <button class="btn btn-dark btn-sm me-2 mb-2 btn-tag" type="button"
                  v-for="(t, index) in g.tags" v-bind:key="index" disabled>
                  {{ t }}
                </button>
              </li>
            </ul>
            <div class="card-footer d-flex justify-content-between bg-gray400">
              <div>
                <span class="fw-bold">Current members:</span> {{ g.currentMembers }}
              </div>
              <button class="btn btn-light btn-outline-dark btn-sm" type="button">Join</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
const API_URL = "https://3000-white-carp-u7p83ovg.ws-us15.gitpod.io";
export default {
  name: "Groupbuy",
  data: function() {
    return {
      groupbuys: []
    };
  },
  mounted: async function() {
    let response = await axios.get(API_URL + "/groupbuy");
    this.groupbuys = response.data;
  },
};
</script>

<style scoped>
.overlay {
  background-color: #e9ecef;
  height: 200px;
  width: 100vw;
  position: relative;
  left: 50%;
  right: 50%;
  margin-left: -50vw;
  margin-right: -50vw;
}

.input-group-btn {
  position: absolute;
  right: 4px;
  top: 4px;
  bottom: 4px;
  z-index: 9;
}

#search-box:focus {
  box-shadow: none;
}

#search-button {
  padding-top: 3px !important;
}

.bg-gray400 {
  background-color: #ced4da;
}

.btn-tag {
    border-radius: 15px;
}
</style>