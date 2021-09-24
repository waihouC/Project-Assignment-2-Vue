<template>
    <transition name="slide-fade">
        <div id="modalOverlay">
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header d-flex justify-content-center">
                        <h3 class="modal-title">Delete Groupbuy</h3>
                    </div>
                    <div class="modal-body">
                        <p>Do you want to delete this group?</p>
                        <h5>{{ groupName }}</h5>
                    </div>
                    <div class="modal-footer d-flex justify-content-center">
                        <button type="button" class="btn btn-danger m-3" @click="confirmDelete">
                            Confirm
                        </button>
                        <button type="button" class="btn btn-secondary m-3" data-bs-dismiss="modal" 
                            @click="closeDelete">
                            Cancel
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </transition>
</template>

<script>
import axios from 'axios';
const API_URL = 'https://3000-white-carp-u7p83ovg.ws-us18.gitpod.io';
export default {
    name: "DeleteGroup",
    props: [ "groupId", "groupName" ],
    methods: {
        closeDelete: function() {
            this.$emit("close-delete-modal");
        },
        confirmDelete: async function() {
            let result = await axios.delete(API_URL + '/groupbuy/delete/' + this.groupId);
            
            if (result.status == 200) {
                this.$emit("group-deleted");
            } else {
                console.log(result);
                this.$emit("error");
            }
        }
    }
}
</script>

<style scoped>
#modalOverlay {
    background-color: rgba(0, 0, 0, 0.5);
    position: fixed;
    width: 100%;
    height: 100vh;
    top: 0px;
    left: 0px;
    padding: 0px;
    margin: 0px;
    z-index: 9999;
    overflow-x: hidden;
}

h3 {
    font-family: "Comic Sans MS", cursive;
}

h5 {
    word-wrap: break-word;
    white-space: normal;
}

.slide-fade-enter-active, .slide-fade-leave-active {
    transition: all .8s;
}

.slide-fade-enter, .slide-fade-leave-to {
    transform: translateY(20px);
    opacity: 0;
}
</style>