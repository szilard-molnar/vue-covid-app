<template>
    <div id="covid-recovered">
        <div class="covid-card" @mouseenter="mouseEnter()" @mouseleave="mouseLeave()">
            <div id="recovered-overlay" class="animate__animated " v-show="showThat"></div>
            <div class="covid-card-header">
                <p>Recovered</p>
            </div>
            <div class="covid-card-body" v-if="selected == null">
                <p>Number of Recovered:<br>
                    <strong>
                        <ICountUp
                            :delay="delay"
                            :endVal="this.covid"
                            :options="options"
                        />
                    </strong>
                </p>
                <p>As of<br><strong>{{date | moment("dddd, MMMM Do YYYY")}}</strong></p>
            </div>

            <div class="covid-card-body" v-if="selected != null">
                <p>Number of Recovered:<br>
                    <strong>
                        <ICountUp
                            :delay="delay"
                            :endVal="this.countryCovid"
                            :options="options"
                        />
                    </strong>
                </p>
                <p>As of<br><strong>{{countryDate | moment("dddd, MMMM Do YYYY")}}</strong></p>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import ICountUp from 'vue-countup-v2';

export default {
    components: {
        ICountUp,
    },

    data() {
        return{
            covid: 0,
            countryCovid: 0,
            date: '',
            countryDate: '',
            showThat: true,

            delay: 0,
            options: {
                useEasing: true,
                useGrouping: true,
                separator: ',',
                decimal: ',',
                prefix: '',
                suffix: '',
            }
        }
    },

    methods: {

        mouseEnter() {
            if(document.getElementById("recovered-overlay").classList.contains("animate__fadeIn"))
            {
                document.getElementById("recovered-overlay").classList.remove("animate__fadeIn");
                document.getElementById("recovered-overlay").classList.add("animate__fadeOut");
            }
            else 
            {
                document.getElementById("recovered-overlay").classList.add("animate__fadeOut");
            }
            setTimeout(function(){
                this.showThis = false;
            }, 750);
        },

        mouseLeave() {
            if(document.getElementById("recovered-overlay").classList.contains("animate__fadeOut"))
            {
                document.getElementById("recovered-overlay").classList.remove("animate__fadeOut");
                document.getElementById("recovered-overlay").classList.add("animate__fadeIn");
            }
            this.showThis = true;
        }
    },

    mounted() {
        if(this.selected == null)
        {
            axios
            .get('https://covid19.mathdro.id/api')
            .then(response => {
                this.covid = response.data.deaths.value;
                this.date = response.data.lastUpdate;
            });
        }

        else if(this.selected != null)
        {
            axios
            .get('https://covid19.mathdro.id/api/countries/' + this.selected)
            .then((response => {
                this.countryCovid = response.data.recovered.value
                this.countryDate = response.data.lastUpdate
            }));
        }
    },

    props: [
        'selected',
    ]
}
</script>

<style lang="scss">
    #covid-recovered {
        margin: auto;
    }

    #covid-recovered #recovered-overlay {
        height: 100%;
        width: 100%;
        background-color: rgba(49, 198, 0, 0.201);
        z-index: 9999;
        border-radius: 20px;
        position: absolute;
    }

    #covid-recovered .covid-card {
        position: relative;
        width: 275px;
        height: 300px;
        background-color: white;
        text-align: center;
        border-radius: 20px;
        box-shadow: 0px 0px 35px 0px rgb(0, 181, 15);
    }

    #covid-recovered .covid-card-header {
        padding: 25px 20px;
        background-color: rgba(4, 33, 252, 0.345);
        border-radius: 20px 20px 0 0;
    }

    #covid-recovered .covid-card-header p {
        margin-bottom: 0;
        font-size: 28px;
        font-weight: 700;
    }

    #covid-recovered .covid-card-body {
        padding: 10px 20px;
        display: grid;
        align-items: center;
        height: 200px;
    }

    #covid-recovered .covid-card-body p {
        margin-bottom: 0;
    }
</style>