<template>
    <div>
        <div id="div-margin">
        </div>
        <div class="border border-secondary rounded-3 p-4 mb-4">
            <h2>Create Groupbuy</h2>
            <form class="mt-4" v-on:submit.prevent="validateForm">
                <div class="row">
                    <div class="col-12 col-lg-9">
                        <label class="form-label required-field" for="userName">User Name:</label>
                        <input class="form-control" type="text" id="userName" v-model="userName" />
                        <div class="validate-msg" v-if="!userNameValid && formSubmitted">
                            Please enter your username.
                        </div>
                    </div>
                    <div class="col-12 col-lg-3 mt-4 mt-lg-0">
                        <label class="form-label required-field" for="contact">Contact:</label>
                        <input class="form-control" type="tel" maxLength="8" id="contact" v-model="contact" />
                        <div class="validate-msg" v-if="!contactValid && formSubmitted">
                            {{validateContactMsg}}
                        </div>
                    </div>
                </div>
                <div class="row">
                    <div class="col-12 col-lg-6 mt-4">
                        <label class="form-label required-field" for="groupName">Group Name:</label>
                        <input class="form-control" type="text" maxLength="100" id="groupName" v-model="groupName" />
                        <div class="validate-msg" v-if="!groupNameValid && formSubmitted">
                            Please enter a group name.
                        </div>
                    </div>
                    <div class="col-12 col-lg-6 mt-4">
                        <label class="form-label required-field" for="location">Location:</label>
                        <input class="form-control" type="text" maxLength="50" id="location" v-model="location" />
                        <div class="validate-msg" v-if="!locationValid && formSubmitted">
                            Please enter a location.
                        </div>
                    </div>
                </div>
                <div class="row">
                    <div class="col-12 col-md-3 mt-4">
                        <label class="form-label required-field" for="price">Price:</label>
                        <div class="d-flex" id="price">
                            <div class="fs-4 me-2">$</div>
                            <input class="form-control me-1" type="number" v-model="priceDollar" @input="maximumDollar" />
                            <div class="fs-4 fw-bold"><sub>.</sub></div>
                            <input class="form-control ms-1" type="number" v-model="priceCent" @input="maximumCent" />
                        </div>
                        <div class="validate-msg" v-if="!priceValid && formSubmitted">
                            Please enter a price.
                        </div>
                    </div>
                    <div class="col-12 col-md-3 mt-4">
                        <label class="form-label required-field" for="maxOrders">Max Orders:</label>
                        <input class="form-control" type="number" id="maxOrders" v-model="maxOrders" @input="validateOrders" />
                        <div class="validate-msg" v-if="!ordersValid && formSubmitted">
                            Please enter maximum orders.
                        </div>
                    </div>
                    <div class="col-12 col-md-3 mt-4">
                        <label class="form-label required-field" for="deadline">Deadline:</label>
                        <datepicker id="deadline" placeholder="Select date" :disabled-dates="state.disabledDates" v-model="deadline"></datepicker>
                        <div class="validate-msg" v-if="!deadlineValid && formSubmitted">
                            Please select a date.
                        </div>
                    </div>
                </div>
                <div class="row">
                    <div class="col-12 col-md-3 mt-4">
                        <label class="form-label required-field" for="category">Category:</label>
                        <select class="form-select" aria-label="Category" id="category" v-model="category">
                            <option value="">Choose a category</option>
                            <option value="Food &amp; Drinks">Food &amp; Drinks</option>
                            <option value="Household">Household</option>
                            <option value="Kids &amp; Babies">Kids &amp; Babies</option>
                            <option value="Electrical &amp; Lifestyle">Electrical &amp; Lifestyle</option>
                        </select>
                        <div class="validate-msg" v-if="!categoryValid && formSubmitted">
                            Please select a category.
                        </div>
                    </div>
                </div>
                <div class="row">
                    <div class="col-12 mt-4">
                        <div class="d-flex" id="subcatFoodDrinks" v-if="category == 'Food &amp; Drinks'">
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Veg &amp; Fruits" 
                                    id="chkVegFruits" v-model="tags">
                                <label class="form-check-label" for="chkVegFruits">
                                    Veg &amp; Fruits
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Meat &amp; Seafood" 
                                    id="chkMeatSeafood" v-model="tags">
                                <label class="form-check-label" for="chkMeatSeafood">
                                    Meat &amp; Seafood
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Dry" 
                                    id="chkDry" v-model="tags">
                                <label class="form-check-label" for="chkDry">
                                    Dry
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Frozen" 
                                    id="chkFrozen" v-model="tags">
                                <label class="form-check-label" for="chkFrozen">
                                    Frozen
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Drinks" 
                                    id="chkDrinks" v-model="tags">
                                <label class="form-check-label" for="chkDrinks">
                                    Drinks
                                </label>
                            </div>
                        </div>
                        <div class="d-flex" id="subcatHousehold" v-if="category == 'Household'">
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Cleaning" 
                                    id="chkCleaning" v-model="tags">
                                <label class="form-check-label" for="chkCleaning">
                                    Cleaning
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Kitchen &amp; Dining" 
                                    id="chkKitchenDining" v-model="tags">
                                <label class="form-check-label" for="chkKitchenDining">
                                    Kitchen &amp; Dining
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Home" 
                                    id="chkHome" v-model="tags">
                                <label class="form-check-label" for="chkHome">
                                    Home
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Bathroom" 
                                    id="chkBathroom" v-model="tags">
                                <label class="form-check-label" for="chkBathroom">
                                    Bathroom
                                </label>
                            </div>
                        </div>
                        <div class="d-flex" id="subcatKidsBabies" v-if="category == 'Kids &amp; Babies'">
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Diapers" 
                                    id="chkDiapers" v-model="tags">
                                <label class="form-check-label" for="chkDiapers">
                                    Diapers
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Food &amp; Milk" 
                                    id="chkFoodMilk" v-model="tags">
                                <label class="form-check-label" for="chkFoodMilk">
                                    Food &amp; Milk
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Nursing" 
                                    id="chkNursing" v-model="tags">
                                <label class="form-check-label" for="chkNursing">
                                    Nursing
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Clothes" 
                                    id="chkClothes" v-model="tags">
                                <label class="form-check-label" for="chkClothes">
                                    Clothes
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Toys" 
                                    id="chkToys" v-model="tags">
                                <label class="form-check-label" for="chkToys">
                                    Toys
                                </label>
                            </div>
                        </div>
                        <div class="d-flex" id="subcatElectricalLifestyle" v-if="category == 'Electrical &amp; Lifestyle'">
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Mobile" 
                                    id="chkMobile" v-model="tags">
                                <label class="form-check-label" for="chkMobile">
                                    Mobile
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Home Appliances" 
                                    id="chkHomeAppliances" v-model="tags">
                                <label class="form-check-label" for="chkHomeAppliances">
                                    Home Appliances
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Kitchen" 
                                    id="chkKitchen" v-model="tags">
                                <label class="form-check-label" for="chkKitchen">
                                    Kitchen
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Lighting" 
                                    id="chkLighting" v-model="tags">
                                <label class="form-check-label" for="chkLighting">
                                    Lighting
                                </label>
                            </div>
                            <div class="form-check me-4">
                                <input class="form-check-input" type="checkbox" value="Stationery" 
                                    id="chkStationery" v-model="tags">
                                <label class="form-check-label" for="chkStationery">
                                    Stationery
                                </label>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="row">
                    <div class="col-12 mt-4">
                        <label class="form-label" for="description">Description:</label>
                        <textarea class="form-control" rows="3" id="description" maxLength="200" v-model="description" />
                    </div>
                </div>
                <div class="row">
                    <div class="col-12 mt-4 d-flex justify-content-center">
                        <button type="submit" class="btn btn-success me-4">Submit</button>
                        <button type="button" class="btn btn-secondary" @click="cancelCreate">Cancel</button>
                    </div>
                </div>
            </form>
        </div>
    </div>
</template>

<script>
import Datepicker from 'vuejs-datepicker';
import moment from 'moment';
import axios from "axios";
const API_URL = "https://3000-white-carp-u7p83ovg.ws-us15.gitpod.io";
export default {
    name: "CreateGroup",
    components: {
        Datepicker
    },
    data: function() {
        return {
            userName: "",
            contact: "",
            groupName: "",
            location: "",
            priceDollar: 0,
            priceCent: 0,
            maxOrders: 1,
            deadline: "",
            category: "",
            tags: [],
            description: "",
            userNameValid: false,
            contactValid: false,
            validateContactMsg: "",
            groupNameValid: false,
            locationValid: false,
            priceValid: false,
            ordersValid: false,
            deadlineValid: false,
            categoryValid: false,
            formSubmitted: false,
            formValid: false,
            state: state
        };
    },
    methods: {
        maximumDollar: function(e) {
            let value = e.target.value;
            if (value.length > 4) {
                value = value.slice(0, 4);
            }
            this.priceDollar = parseInt(value);
        },
        maximumCent: function(e) {
            let value = e.target.value;
            if (value.length > 2) {
                value = value.slice(0, 2);
            }
            this.priceCent = parseInt(value);
        },
        validateOrders: function(e) {
            // accept orders value btw 1 - 9999
            let value = e.target.value;
            if (parseInt(value) < 1) {
                value = "1";
            }
            if (value.length > 4) {
                value = value.slice(0, 4);
            }
            this.maxOrders = parseInt(value);
        },
        validateForm: function(e) {
            this.formSubmitted = true;

            this.userNameValid = this.userName ? true : false;
            this.groupNameValid = this.groupName ? true: false;
            this.locationValid = this.location ? true : false;
            this.priceValid = this.priceDollar || this.priceCent ? true : false;
            this.ordersValid = this.maxOrders ? true : false;
            this.deadlineValid = this.deadline ? true : false;
            this.categoryValid = this.category ? true : false;

            if (!this.contact) {
                this.validateContactMsg = "Please enter your contact.";
                this.contactValid = false;
            } else if (!this.isValidPhoneNumber(this.contact)) {
                this.validateContactMsg = "Contact is not valid.";
                this.contactValid = false;
            } else {
                this.validateContactMsg = "";
                this.contactValid = true;
            }

            if (this.userNameValid && this.contactValid && this.groupNameValid &&
                this.locationValid && this.priceValid && this.ordersValid &&
                this.categoryValid && this.deadlineValid) {
                console.log("form validated.");
                this.formValid = true;
                this.insertGroup();
            }

            e.preventDefault();
        },
        isValidPhoneNumber: function(n) {
            var regex = /^\d{8}$/;
            return regex.test(n);
        },
        cancelCreate: function() {
            this.$emit("cancel-create");
        },
        insertGroup: async function() {
            if (this.formValid) {
                console.log("form is valid.");
                let result = await axios.post(API_URL + '/groupbuy/create', {
                    userName: this.userName,
                    groupName: this.groupName,
                    price: this.priceDollar + (this.priceCent / 100),
                    location: this.location,
                    deadline: this.deadline,
                    contact: this.contact,
                    maxOrders: this.maxOrders,
                    description: this.description,
                    category: this.category,
                    tags: this.tags
                });

                if (result.status == 200) {
                    console.log("200: success");
                    this.$emit("new-group-created");
                } else {
                    // redirect error page?
                    console.log(result);
                }
            }
        }
    }
}

// control datepicker valid dates
var state = {
    disabledDates: {
        to: moment().add(1, 'days').toDate()
    }
};
</script>

<style scoped>
#div-margin {
    height: 24px;
}

h2 {
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
</style>