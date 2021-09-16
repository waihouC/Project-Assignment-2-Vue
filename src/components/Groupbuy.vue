<template>
    <div>
        <h1>Groupbuy List</h1>
        <div class="border border-secondary rounded-3 p-4">
            <div class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4">
                <div class="col" v-for="g in groupbuys" v-bind:key="g._id">
                    <div class="card border-dark h-100">
                        <div class="card-header bg-gray400">
                            <h4>{{g.groupName}}</h4>
                        </div>
                        <div class="card-body">
                            <h5 class="card-title">{{g.userName}}</h5>
                            <h6 class="card-subtitle mb-2 text-muted">{{g.contact}}</h6>
                            <p class="card-text">{{g.remarks}}</p>
                        </div>
                        <ul class="list-group list-group-flush">
                            <li class="list-group-item"><span class="fw-bold">Category:</span> {{g.category}}</li>
                            <li class="list-group-item"><span class="fw-bold">Price:</span> ${{ g.price.$numberDecimal }}</li>
                            <li class="list-group-item"><span class="fw-bold">Location:</span> {{g.location}}</li>
                            <li class="list-group-item"><span class="fw-bold">Max Orders:</span> {{g.maxOrders}}</li>
                            <li class="list-group-item"><span class="fw-bold">Deadline:</span> {{g.deadline | formatDate}}</li>
                        </ul>
                        <div class="card-body py-2 d-flex flex-row" >
                            <button class="btn btn-light btn-sm btn-tag" type="button" v-for="(t, index) in g.tags" v-bind:key="index">{{t}}</button>
                        </div>
                        <div class="card-footer d-flex justify-content-between bg-gray400">
                            <div>
                                <span class="fw-bold">Current members:</span> {{g.currentMembers}}
                            </div>
                            <button class="btn btn-success btn-sm" type="button">Join</button>
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
        //console.log(response.data);
    },
}
</script>

<style scoped>
.bg-gray400 {
    background-color: #ced4da;
}

.btn-tag {
    border-radius: 30%;
}
</style>