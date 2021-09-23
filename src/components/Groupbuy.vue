<template>
  <div>
    <div class="overlay">
      <div class="container-lg">
        <form class="p-4">
          <div class="input-group">
            <input type="search" class="form-control rounded-2" placeholder="Search here..."
              id="search-box" aria-label="Search" aria-describedby="search-button" v-model="queryTerm" />
            <div class="input-group-btn">
              <button class="btn btn-secondary btn-sm" type="submit" id="search-button" @click="performSearch">
                <img src="../assets/img/search-symbol.png" />
              </button>
            </div>
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
              <div class="dropdown">
                <a class="btn btn-secondary btn-sm dropdown-toggle" href="#" role="button"
                 id="dropdownMemberList" data-bs-toggle="dropdown" aria-expanded="false"
                 v-bind:class="{disabled: g.groupMembers.length == 0}">
                  {{ g.groupMembers.length }} members
                </a>
                <ul class="dropdown-menu" aria-labelledby="dropdownMemberList">
                  <li>
                    <a class="dropdown-item lh-1 dropdown-option" href="#" 
                      v-for="m in g.groupMembers" 
                      v-bind:key="m._id"
                      v-on:click="showMember(m._id)">
                      <span class="spanName">{{ m.firstName }}</span>, <span class="spanName">{{ m.lastName }}</span>
                    </a>
                  </li>
                </ul>
              </div>
              <button class="btn btn-light btn-outline-dark btn-sm" type="button" 
                v-bind:class="{disabled: g.groupMembers.length >= g.maxOrders}" 
                @click="showJoinForm(g._id)">Join
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <JoinForm
      v-if="showForm"
      v-bind:mode="joinMode"
      v-bind:groupId="groupId"
      v-bind:selectedMember="memberDetails"
      v-on:close-join-form="closeJoinForm"
      v-on:new-member-added="closeJoinForm"
      v-on:error="joinError"
    />
  </div>
</template>

<script>
import JoinForm from './JoinForm';
import axios from 'axios';
const API_URL = 'https://3000-white-carp-u7p83ovg.ws-us17.gitpod.io';
export default {
  name: "Groupbuy",
  components: {
    JoinForm
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
      memberDetails: {}
    };
  },
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
    },
    joinError: function() {
      this.showForm = false;
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
  padding-top: 3px !important;
}

.bg-gray400 {
  background-color: #ced4da;
}

.btn-tag {
    border-radius: 15px;
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
</style>