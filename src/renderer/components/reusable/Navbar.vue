<template>
    <div>
        <b-navbar variant="dark" type="dark" class="navbar1">

            <!-- tooltip & button for show time -->
            <b-navbar-brand>
                <b-button
                    pill
                    v-b-toggle.collapse-time
                    variant="primary"
                    style="margin-right: 10px;"
                    class="ml-auto"
                    id="ShowTime"
                >Show Time</b-button>    
            </b-navbar-brand>
            <b-tooltip target="ShowTime" triggers="hover">
                Toggle
                <b>current time</b> bar
            </b-tooltip>

            <!-- tooltip & button for add new plant -->
            <b-navbar-nav class="ml-auto">
                <b-button 
                    pill 
                    v-b-modal.modal-xl-addplant 
                    variant="success" 
                    id="AddNewPlant"
                >Add New Plant</b-button>
            </b-navbar-nav>
            <b-tooltip target="AddNewPlant" triggers="hover">
                Add a
                <b>new plant</b> to the tray
            </b-tooltip>

            <!-- add new plant modal -->
            <b-modal
                id="modal-xl-addplant"
                ref="modal-xl-addplant"
                header-bg-variant="success"
                no-close-on-backdrop
                centered
                scrollable
                hide-footer
                no-close-on-esc
                title=" 🎍 New Plant"
            >
                <div>
                    <b-form @submit="onSubmit(); RefreshCards(); makeToast('primary')" @reset="onReset()">
                        <!-- Plant name -->
                        <b-form-group
                            label="Plant name:"
                            label-for="input-1"
                            description="Enter your plant name here."
                        >
                            <b-form-input
                                id="input-1"
                                v-model="NewPlantForm.PlantName"
                                required
                                placeholder="Enter plant name"
                            ></b-form-input>
                        </b-form-group>

                        <!-- Dominant radio -->
                        <b-form-group label="Select plant dominant:" >
                            <b-form-radio-group name="radio-dominant" required>
                                <b-form-radio
                                    size="lg"
                                    v-model="NewPlantForm.PlantDominant"
                                    name="Indica-radio"
                                    value="Indica"
                                >Indica</b-form-radio>
                                
                                <b-form-radio
                                    size="lg"
                                    v-model="NewPlantForm.PlantDominant"
                                    name="Sativa-radio"
                                    value="Sativa"
                                >Sativa</b-form-radio>
                                
                                <b-form-radio
                                    size="lg"
                                    v-model="NewPlantForm.PlantDominant"
                                    name="Hybrid-radio"
                                    value="Hybrid"
                                >Hybrid</b-form-radio>
                            </b-form-radio-group>
                        </b-form-group>

                        <!-- Seed type radio -->
                        <b-form-group label="Select seed type:">
                            <b-form-radio-group name="radio-seed" required>
                                <b-form-radio
                                    size="lg"
                                    v-model="NewPlantForm.PlantSeedType"
                                    name="Feminized-radio"
                                    value="Feminized"
                                >Feminized</b-form-radio>

                                <b-form-radio
                                    size="lg"
                                    v-model="NewPlantForm.PlantSeedType"
                                    name="Autoflower-radio"
                                    value="Autoflower"
                                >Autoflower</b-form-radio>

                                <b-form-radio
                                    size="lg"
                                    v-model="NewPlantForm.PlantSeedType"
                                    name="CBD-radio"
                                    value="CBD"
                                >CBD</b-form-radio>
                            </b-form-radio-group>
                        </b-form-group>

                        <!-- Seed price -->
                        <b-form-group
                            label="Enter seed price:"
                            label-for="input-2"
                            description="Enter your seed price here."
                        >
                            <b-form-input
                                type="number"
                                required
                                id="input-2"
                                v-model="NewPlantForm.PlantSeedPrice"
                                placeholder="Enter seed price"
                            ></b-form-input>

                        </b-form-group>

                        <!-- Amount -->
                        <b-form-group
                            label="Enter plant amount:"
                            label-for="input-3"
                            description="Enter your plant amount here."
                        >
                            <b-form-input
                                type="number"
                                required
                                id="input-3"
                                v-model="NewPlantForm.PlantAmount"
                                placeholder="Enter plant amount"
                            ></b-form-input>

                        </b-form-group>

                        <!-- Plant date -->
                  
                            <label for="datepicker-full-width">Set germination date:</label>
                            <b-datepicker required today-variant="success" selected-variant="success" v-model="NewPlantForm.PlantGermDate"></b-datepicker>
             
                        <hr />

                        <b-button pill type="reset" variant="dark">Reset</b-button>
                        <b-button pill type="submit" variant="success">Submit</b-button>
                    </b-form>
                </div>
            </b-modal>
        </b-navbar>

        <b-collapse id="collapse-time" class="text-center">
            <b-card
                bg-variant="primary"
                text-variant="white"
                class="text-center"
                style="font-size: 20px;"
            >{{currtime}}</b-card>
        </b-collapse>
    </div>
</template>

<script>
    // electron-db
    import db from "electron-db";
    // moment.js
    import moment from "moment";
    import path from "path";
    import { log } from "util";

    // path to database file
    const location = path.join(__dirname, "../");

    export default {
        name: "navbars",
        data() {
            return {
                // moment.js time
                currtime: moment().format("MMMM Do YYYY, h:mm:ss a"),

                // new plant form obj
                NewPlantForm: {
                    PlantName: "",
                    PlantDominant: "",
                    PlantGermDate: "",
                    PlantSeedType: "",
                    PlantSeedPrice: null,
                    PlantAmount: null,
                    CurrentWeek: null
                }
            }
        },
        methods: {
            // Toast for successful insertion
            makeToast() {
                this.$bvToast.toast("1 New plant added", {
                    title: "New plant created",
                    toaster: "b-toaster-bottom-left",
                    variant: "success",
                    solid: true,
                    autoHideDelay: 2000
                })
            },
            // On form submit
            onSubmit() {
                console.log(this.NewPlantForm.PlantName);

                if (db.valid("plantsDB", location)) {
                    

                    // create new plant object
                    let obj = new Object()
                    obj.Pname = this.NewPlantForm.PlantName
                    obj.dominant = this.NewPlantForm.PlantDominant
                    obj.SeedCost = this.NewPlantForm.PlantSeedPrice
                    obj.GermDate = this.NewPlantForm.PlantGermDate
                    obj.NumberOfPlants = this.NewPlantForm.PlantAmount
                    obj.PlantSeedType = this.NewPlantForm.PlantSeedType

                    // insert it into table (plantsDB)
                    db.insertTableContent("plantsDB", location, obj, (succ, msg) => {
                        console.log("Insertion Success: " + succ)
                        console.log("Insertion Message: " + msg)
                    })
                }

                // clean up input
                this.NewPlantForm.PlantName = ""
                this.NewPlantForm.PlantDominant = ""
                this.NewPlantForm.PlantGermDate = null
                this.NewPlantForm.PlantSeedType = ""
                this.NewPlantForm.PlantSeedPrice = null
                this.NewPlantForm.PlantAmount = null

                // Trick to reset/clear native browser form validation state
                this.show = false;
                this.$nextTick(() => {
                    this.show = true;
                });

                // hide add plant modal
                this.$refs["modal-xl-addplant"].hide();
            },
            // On from reset
            onReset() {
                this.NewPlantForm.PlantName = "";
                this.NewPlantForm.PlantDominant = "";
                this.NewPlantForm.PlantGermDate = null;
                this.NewPlantForm.PlantGender = "";

                // Trick to reset/clear native browser form validation state
                this.show = false;
                this.$nextTick(() => {
                    this.show = true;
                });
            },
            RefreshCards() {
                // call parent component method to reload from db
                this.$emit("refresh");
            }
        },
        mounted() {
            // turn on the clock
            setInterval(
                function() {
                    this.currtime = moment().format("MMMM Do YYYY, h:mm:ss a");
                }.bind(this), 
                1000
            )
        }
    }
</script>

<style>
    #modal-xl-addplant___BV_modal_content_ {
        border-radius: 2em;
    }

</style>