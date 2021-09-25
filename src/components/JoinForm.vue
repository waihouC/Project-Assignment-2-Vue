<template>
    <transition name="slide-fade">
        <div id="formOverlay">
            <div id="formOverlayContent">
                <button type="button" class="btn-close" id="btnCloseForm" aria-label="Close" 
                    v-if="mode == 'add'" @click="closeForm">
                </button>
                <div>
                    <h2 v-if="mode == 'add'">Tompang to this group</h2>
                    <h2 v-if="mode == 'show'">View Member Details</h2>
                    <form v-on:submit.prevent="validateForm">
                        <div class="my-2">
                            <label class="form-label required-field" for="firstName">First Name:</label>
                            <input class="form-control" type="text" maxLength="30" id="firstName" 
                                v-model="firstName" :readonly="mode == 'show'" />
                            <div class="validate-msg" v-if="!isFirstNameValid && formSubmitted">
                                Please enter your first name.
                            </div>
                        </div>
                        <div class="my-2">
                            <label class="form-label required-field" for="lastName">Last Name:</label>
                            <input class="form-control" type="text" maxLength="30" id="lastName" 
                                v-model="lastName" :readonly="mode == 'show'" />
                            <div class="validate-msg" v-if="!isLastNameValid && formSubmitted">
                                Please enter your last name.
                            </div>
                        </div>
                        <div class="my-2">
                            <label class="form-label required-field" for="contact">Contact:</label>
                            <input class="form-control" type="tel" maxLength="8" id="contact" 
                                v-model="contact" :readonly="mode == 'show'" />
                            <div class="validate-msg" v-if="!isContactValid && formSubmitted">
                                {{ validateContactMsg }}
                            </div>
                        </div>
                        <div class="d-grid col-6 mx-auto mt-5">
                            <button class="btn btn-secondary" type="submit" v-if="mode == 'add'">
                                Submit
                            </button>
                            <button class="btn btn-secondary" type="button" 
                                v-if="mode == 'show'" @click="closeForm">OK</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </transition>    
</template>

<script>
import axios from 'axios';
const API_URL = 'https://project-assignment-2-express.herokuapp.com';
export default {
    name: "JoinForm",
    data: function() {
        return {
            firstName: this.selectedMember.firstName || "",
            lastName: this.selectedMember.lastName || "",
            contact: this.selectedMember.contact || "",
            formSubmitted: false,
            formValid: false,
            isFirstNameValid: false,
            isLastNameValid: false,
            isContactValid: false,
            validateContactMsg: ""
        }
    },
    props: [ "mode", "groupId", "selectedMember" ],
    methods: {
        closeForm: function() {
            this.$emit("close-join-form");
        },
        validateForm: function(e) {
            this.formSubmitted = true;

            this.isFirstNameValid = this.firstName ? true : false;
            this.isLastNameValid = this.lastName ? true : false;
            if (!this.contact) {
                this.validateContactMsg = "Please enter your contact.";
                this.isContactValid = false;
            } else if (!this.isValidPhoneNumber) {
                this.validateContactMsg = "Contact is not valid.";
                this.isContactValid = false;
            } else {
                this.validateContactMsg = "";
                this.isContactValid = true;
            }

            if (this.isFirstNameValid && 
                this.isLastNameValid &&
                this.isContactValid) {
                    this.formValid = true;
                    this.submitForm();
            }

            e.preventDefault();
        },
        submitForm: async function() {
            if (this.formValid) {
                let result = await axios.put(API_URL + "/groupbuy/join/" + this.groupId, {
                    'firstName': this.firstName,
                    'lastName': this.lastName,
                    'contact': this.contact
                })

                if (result.status == 200) {
                    this.$emit("new-member-added");
                } else {
                    console.log(result);
                    this.$emit("error");
                }
            }
        },
        
    },
    computed: {
        isValidPhoneNumber: function() {
            var regex = /^\d{8}$/;
            return regex.test(this.contact);
        }
    }
}
</script>

<style scoped>
#formOverlay {
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

#formOverlayContent {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: white;
    border-radius: 1em;
    padding: 2em;
}

#btnCloseForm {
    position: absolute;
    top: 10px;
    right: 10px;
}

h2 {
    margin: 22px 0px;
    font-family: "Comic Sans MS", cursive;
}

.required-field::after {
    content: '*';
    color: red;
    position: relative;
    right: -5px;
    top: 0;
}

input:focus {
    box-shadow: none;
}

.validate-msg {
    color: red;
    font-size: .875em;
    margin-top: .2em;
}

.slide-fade-enter-active, .slide-fade-leave-active {
    transition: all .8s;
}

.slide-fade-enter, .slide-fade-leave-to {
    transform: translateY(20px);
    opacity: 0;
}
</style>
