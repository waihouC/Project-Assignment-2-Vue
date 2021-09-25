<template>
  <div>
    <div class="overlay">
      <div class="container-lg">
        <form class="p-4" v-on:submit.prevent="performSearch">
          <div class="d-flex">
            <div class="input-group me-3">
              <input type="search" class="form-control rounded-2" placeholder="Search here..."
                id="search-box" aria-label="Search" aria-describedby="search-button" v-model="queryTerm" />
              <div class="input-group-btn">
                <button class="btn btn-secondary btn-sm" type="submit" id="search-button">
                  <i class="bi bi-search"></i>
                </button>
              </div>
            </div>
            <button class="btn btn-outline-dark btn-sm" type="submit" id="refresh-button" @click="refreshSearch">
              <i class="bi bi-arrow-clockwise"></i>
            </button>
          </div>
          <div class="btn-group mt-2 mt-md-3" role="group" aria-label="product tags toggle">
            <div class="form-check mx-2">
              <input class="form-check-input check-success" type="radio" name="queryType" id="groupNameQuery" 
                value="groupName" v-model="queryType" />
              <label class="form-check-label" for="groupNameQuery">
                Product
              </label>
            </div>
            <div class="form-check mx-2">
              <input class="form-check-input check-success" type="radio" name="queryType" id="locationQuery" 
                value="location" v-model="queryType" />
              <label class="form-check-label" for="locationQuery">
                Location
              </label>
            </div>
            <div class="form-check mx-2">
              <input class="form-check-input" type="radio" name="queryType" id="tagsQuery"
                value="tags" v-model="queryType" />
              <label class="form-check-label" for="tagsQuery">
                Tag
              </label>
            </div>
          </div>
          <div class="row d-flex justify-content-around align-items-center">
            <div class="col-12 col-md-5 my-2 mt-md-4">
              <select class="form-select" aria-label="Category" v-model="queryCategory">
                <option value="">Choose a category</option>
                <option value="Food &amp; Drinks">Food &amp; Drinks</option>
                <option value="Household">Household</option>
                <option value="Kids &amp; Babies">Kids &amp; Babies</option>
                <option value="Electrical &amp; Lifestyle">Electrical &amp; Lifestyle</option>
              </select>
            </div>
            <div class="col-12 col-md-5 my-2 mt-md-4">
              <div class="d-flex justify-content-between">
                <label for="priceRangeCtrl" class="form-label">Price range: (0 - 10,000)</label>
                <label class="form-label fw-bold">${{ queryPrice }}</label>
              </div>
              <input type="range" class="form-range" min="0" max="10000" id="priceRangeCtrl" v-model="queryPrice">
            </div>
          </div>
        </form>
      </div>
    </div>
    <div class="border border-secondary rounded-3 p-4 pt-2 my-4">
      <div class="mb-3">
        <span class="fw-bold">{{ this.groupbuys.length }}</span> groups available:
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
                <span class="badge bg-secondary rounded-pill me-2 mb-2 p-2"
                  v-for="(t, index) in g.tags" v-bind:key="index">
                  {{ t }}
                </span>
              </li>
            </ul>
            <div class="card-footer d-flex justify-content-between bg-gray400">
              <div class="dropdown">
                <a class="btn btn-secondary btn-sm dropdown-toggle" href="#" role="button"
                 id="dropdownMemberList" data-bs-toggle="dropdown" aria-expanded="false"
                 v-bind:class="{disabled: g.groupMembers.length == 0}">
                  {{ g.groupMembers.length }} members
                </a>
                <ul class="dropdown-menu" aria-labelledby="dropdownMemberList">
                  <li>
                    <a class="dropdown-item lh-1 dropdown-option" 
                      v-for="m in g.groupMembers" 
                      v-bind:key="m._id"
                      v-on:click="showMember(m._id)">
                      <span class="spanName">{{ m.firstName }}</span>, <span class="spanName">{{ m.lastName }}</span>
                    </a>
                  </li>
                </ul>
              </div>
              <div id="btn-menu">
                <button class="btn btn-success btn-sm pt-0 ms-2" type="button" 
                  v-if="g.groupMembers.length < g.maxOrders" @click="showJoinForm(g._id)">
                  <i class="bi bi-cart-check"></i>
                </button>
                <button type="button" class="btn btn-primary btn-sm pt-0 ms-2" 
                  v-if="userLoggedIn" @click="editGroup(g._id)">
                  <i class="bi bi-pencil-square"></i>
                </button>
                <button type="button" class="btn btn-danger btn-sm pt-0 ms-2" 
                  v-if="userLoggedIn" @click="deleteGroup(g._id, g.groupName)">
                  <i class="bi bi-trash"></i>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <JoinForm
      :mode="joinMode"
      :groupId="groupId"
      :selectedMember="memberDetails"
      v-if="showForm"
      v-on:close-join-form="closeJoinForm"
      v-on:new-member-added="onMemberAdded"
      v-on:error="joinError"
    />
    <DeleteGroup 
      :groupId="groupId"
      :groupName="groupToDelete"
      v-if="showDelete"
      v-on:close-delete-modal="closeDeleteModal"
      v-on:group-deleted="onGroupDeleted"
      v-on:error="deleteError"
    />
  </div>
</template>

<script>
import JoinForm from './JoinForm';
import DeleteGroup from './DeleteGroup';
import axios from 'axios';
const API_URL = 'https://project-assignment-2-express.herokuapp.com';
export default {
  name: "Groupbuy",
  components: {
    JoinForm,
    DeleteGroup
  },
  data: function() {
    return {
      groupbuys: [],
      queryTerm: "",
      queryType: "groupName",
      queryCategory: "",
      queryPrice: 0,
      showForm: false,
      joinMode: "add",
      groupId: "",
      memberDetails: {},
      showDelete: false,
      groupToDelete: ""
    };
  },
  props: [ "userLoggedIn" ],
  mounted: async function() {
    let response = await axios.get(API_URL + '/groupbuy');
    this.groupbuys = response.data;
  },
  methods: {
    performSearch: async function() {
      let queryGroupName;
      let queryLocation;
      let queryTags;
      let queryCategory;
      let queryPrice;

      if (this.queryType == "groupName") {
        queryGroupName = this.queryTerm;
      } else if (this.queryType == "location") {
        queryLocation = this.queryTerm;
      } else if (this.queryType == "tags") {
        queryTags = this.queryTerm;
      }

      if (this.queryCategory != "") {
        queryCategory = this.queryCategory;
      }

      if (this.queryPrice > 0) {
        queryPrice = this.queryPrice;
      }

      let response = await axios.get(API_URL + '/groupbuy/search', {
        params: {
          groupName: queryGroupName,
          location: queryLocation,
          tags: queryTags,
          category: queryCategory,
          price: queryPrice
        }
      });

      this.groupbuys = response.data;
    },
    refreshSearch: function() {
      this.queryTerm = "";
      this.queryType = "groupName";
      this.queryCategory = "";
      this.queryPrice = 0;
    },
    showJoinForm: function(id) {
      this.showForm = true;
      this.joinMode = "add";
      this.groupId = id;
      this.memberDetails = {
        firstName: "",
        lastName: "",
        contact: ""
      }
    },
    closeJoinForm: function() {
      this.showForm = false;
      this.groupId = "";
    },
    onMemberAdded: function() {
      this.showForm = false;
      this.groupId = "";
      this.$emit("force-rerender");
    },
    joinError: function() {
      this.showForm = false;
      this.groupId = "";
      alert("Sorry. Error joining group. Please try again later.");
    },
    showMember: async function(id) {
      let response = await axios.get(API_URL + '/groupbuy/join/' + id);
      this.memberDetails = {
        firstName: response.data.groupMembers[0].firstName,
        lastName: response.data.groupMembers[0].lastName,
        contact: response.data.groupMembers[0].contact
      }
      
      this.showForm = true;
      this.joinMode = "show";
    },
    editGroup: function(id) {
      this.$emit("edit-group", id);
    },
    deleteGroup: function(id, name) {
      this.showDelete = true;
      this.groupId = id;
      this.groupToDelete = name;
    },
    closeDeleteModal: function() {
      this.showDelete = false;
      this.groupId = "";
    },
    onGroupDeleted: function() {
      this.showDelete = false;
      this.groupId = "";
      this.$emit("force-rerender");
    },
    deleteError: function() {
      this.showDelete = false;
      this.groupId = "";
      alert("Sorry. Error deleting group. Please try again later.");
    }
  }
};
</script>

<style scoped>
.overlay {
  background-color: #e9ecef;
  height: 240px;
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
  top: -3px;
  left: 2px;
}

.bg-gray400 {
  background-color: #ced4da;
}

.dropdown-option {
  max-width: 200px;
}

.spanName {
  display: inline-block;
  max-width: 50%;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

i {
  font-size: 1.1rem;
}
</style>