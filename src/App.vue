<template>
  <div id="app">
    <div class="title">
          <h3>Covid Tracker Vue.js</h3>
    </div>

    <div class="selected-country animate__animated " v-if="selected == null">
      <p>Globally</p>
    </div>
    <div class="selected-country animate__animated " v-else>
      <p>{{selected}}</p>
    </div>

    <div class="dropdown-responsive animate__animated ">
      <b-form-select v-model="selected" v-on:change="changeCountry()">
            <template #first>
                <b-form-select-option :value="null" disabled>
                    -- Please select a country --
                </b-form-select-option>
            </template>

            <b-form-select-option v-for="(country, i) in countries" :key="i" :value="country.name">{{country.name}}</b-form-select-option>
        </b-form-select>
    </div>

    <div class="cards animate__animated ">
      <Confirmed :selected="selected" :key="confirmedKey+'B'"/>
      <Recovered :selected="selected" :key="recoveredKey"/>
      <Dead :selected="selected" :key="deadKey+'A'"/>
    </div>
    <div class="dropdown animate__animated ">
      <b-form-select v-model="selected" v-on:change="changeCountry()">
            <template #first>
                <b-form-select-option :value="null" disabled>
                    -- Please select a country --
                </b-form-select-option>
            </template>

            <b-form-select-option v-for="(country, i) in countries" :key="i" :value="country.name">{{country.name}}</b-form-select-option>
        </b-form-select>
    </div>
  </div>
</template>

<script>
import Confirmed from './components/Confirmed';
import Dead from './components/Dead';
import Recovered from './components/Recovered';

import axios from 'axios';

export default {
  name: 'App',
  components: {
    Confirmed,
    Dead,
    Recovered,
  },

  data() {
    return {
      countries: '',
      selected: null,

      allData: '',

      deadKey: 0,
      recoveredKey: 0,
      confirmedKey: 0,
    }
  },

  mounted() {
    document.querySelector(".cards").classList.add("animate__zoomIn");
    document.querySelector(".dropdown").classList.add("animate__fadeInUp");
    document.querySelector(".selected-country").classList.add("animate__fadeInDown");

    this.countryRequest();
  },

  methods: {
    countryRequest() {
            axios
                .get('https://covid19.mathdro.id/api/countries')
                .then(response => this.countries = response.data.countries);
    },

    changeCountry() {
        this.selected = event.target.value;

        this.deadKey++;
        this.recoveredKey++;
        this.confirmedKey++;
    },
  }
}
</script>

<style lang="scss"> 
  #app {
    height: 100vh;
    background-color: rgb(255, 255, 180);
  }

  .title {
    height: 75px;
    border-bottom: 2px solid rgba(0, 60, 255, 0.612);
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .cards {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    column-gap: 20px;
    width: 90%;
    margin: auto;
    margin-top: 35px;
  }

  .dropdown {
    margin: auto;
    margin-top: 50px;
    max-width: 500px;
  }

  .dropdown-responsive {
    display: none;
    margin: auto;
    margin-top: 50px;
    max-width: 500px;
  }

  .selected-country {
    margin-top: 25px;
  }

  .selected-country p {
    margin-bottom: 0;
    font-size: 32px;
    font-weight: 700;
    border-bottom: 2px solid black;
    text-align: center;
    max-width: fit-content;
    margin: auto;
  }

  @media (max-width: 940px) {
    .cards {
      grid-template-columns: 1fr;
      row-gap: 40px;
    }

    .dropdown {
      display: none;
    }

    .dropdown-responsive {
      display: block;
    }
  }

  @media (max-width: 550px) {
    .dropdown-responsive {
      max-width: 300px;
    }
  }
</style>
