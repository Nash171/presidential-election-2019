<template>
  <div id="app">
    <Header/>
    <div class="overview-container">
      <div class="overview">
        <select v-model="selectedDistrict">
          <option v-for="district in districts" :key="district.id"  v-bind:value="district.id">
            {{ district.name }}
          </option>
        </select>
        <div class="record-container">
          <div class="record" v-for="record in districtResults[selectedDistrict]" :key="record.candidateId">
            <div class="candidate">
              <img :src="require('./assets/logo/'+record.candidateId+'.png')" />
            </div>
            <div class="votes">
              {{ record.votes.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") }}
            </div>
            <div class="precentage">
              {{ record.precentage }}%
            </div>
          </div>
        </div>
      </div>
      <div class="map">
        <Map :overlay="overlay"/>
      </div>
    </div>
    <Results :results="results" :districts="districts" :selectedDistrict="selectedDistrict"/>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import Map from './components/Map.vue'
import Results from './components/Results.vue'
import axios from "axios";

const candidates = {
  1: "Gotabaya Rajapaksa",
  2: "Sajith Premadasa",
  3: "Anura Kumara Dissanayaka",
  4: "Other"
}

export default {
  name: 'app',
  components: {
    Header,
    Map,
    Results
  },
  data: function() {
		return {
      overlay: [],
      districts: [
        {
          id: 0,
          name: 'All Island',
        },
        {
          id: 1,
          name: 'Colombo',
        },
        {
          id: 2,
          name: 'Gampaha',
        },
        {
          id: 3,
          name: 'Kaluthara',
        },
        {
          id: 4,
          name: 'Kandy',
        },
        {
          id: 5,
          name: 'Matale',
        },
        {
          id: 6,
          name: 'Nuwara Eliya',
        },
        {
          id: 7,
          name: 'Galle',
        },
        {
          id: 8,
          name: 'Matara',
        },
        {
          id: 9,
          name: 'Hambantota',
        },
        {
          id: 10,
          name: 'Jaffna',
        },
        {
          id: 11,
          name: 'Vanni',
        },
        {
          id: 12,
          name: 'Batticaloa',
        },
        {
          id: 13,
          name: 'Digamadulla',
        },
        {
          id: 14,
          name: 'Trincomalee',
        },
        {
          id: 15,
          name: 'Kurunegala',
        },
        {
          id: 16,
          name: 'Puttalam',
        },
        {
          id: 17,
          name: 'Anuradhapura',
        },
        {
          id: 18,
          name: 'Polonnaruwa',
        },
        {
          id: 19,
          name: 'Badulla',
        },
        {
          id: 20,
          name: 'Moneragala',
        },
        {
          id: 21,
          name: 'Ratnapura',
        },
        {
          id: 22,
          name: 'Kegalle',
        },
      ],
      selectedDistrict: 0,
      results: [],
      districtResults: [],
    }
  },
  created () {
    this.fetchResults();
    this.timer = setInterval(this.fetchResults, 60000)
  },
  methods: {
    track () {
      this.$ga.page('/')
    },
    fetchResults () {
      axios
      .get("data/results.json")
      .then(res => {

        const district_results = Array(23).fill(null).map(()=> ({
          1: 0,
          2: 0,
          3: 0,
          4: 0
        }));

        const results = res.data.results.map((result, index) => {
          const total = result.count.reduce((a, b) => a + b.votes, 0)
          const districtId = Math.floor(result.divisionId/100);

          const count = result.count.map((record,index) => {
            district_results[0][record.candidate] += record.votes;
            district_results[districtId][record.candidate] += record.votes;

            return {
              "id": index,
              "candidate": candidates[record.candidate],
              "votes": record.votes,
              "precentage": (record.votes*100 / total).toFixed(2)
            }
          });

          count.sort((a,b)=>b.votes-a.votes);

          return {
              "id": index,
              "divisionId": result.divisionId,
              "divisionName": result.divisionName,
              "districtId": districtId,
              "count": count
          }
        })

        const overlay = [];
        const districtResults = district_results.map((district,index) => {
          const total = district[1]+district[2]+district[3]+district[4];

          const _district = [{
            "candidateId": 1,
            "votes": district[1],
            "precentage": total ? (district[1]*100 / total).toFixed(2) : 0
          },
          {
            "candidateId": 2,
            "votes": district[2],
            "precentage": total ? (district[2]*100 / total).toFixed(2) : 0
          },
          {
            "candidateId": 3,
            "votes": district[3],
            "precentage": total ? (district[3]*100 / total).toFixed(2) : 0
          },
          {
            "candidateId": 4,
            "votes": district[4],
            "precentage": total ? (district[4]*100 / total).toFixed(2) : 0
          }];

          _district.sort((a,b)=>b.votes-a.votes);

          if(index){
            overlay.push({
              id: index,
              lead: _district[0].candidateId,
              precentage: _district[0].precentage
            })
          }

          return _district;
        });

        this.results = results.reverse();
        this.districtResults = districtResults;
        this.overlay = overlay;
      })
    },
  },
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    display: flex;
    flex-direction: column;
  }

  @media only screen and (min-width: 600px) {
    .overview-container {
      display: flex;
      flex-direction: row;
      align-self: center;
      max-width: 800px;
    }
    .overview {
      width: 50%;
      padding: 30px;
    }
    .map {
      width: 50%;
    }
  }

  @media only screen and (max-width: 599px) {
    .overview-container {
      display: flex;
      flex-direction: column;
      align-self: center;
      max-width: 800px;
    }
    .overview {
      padding: 30px;
    }
  }

  select {
    width: 100%;
    height: 30px;
    font-size: 16px;
  }
  .record-container {
    padding: 10px;
  }
  .record {
    display: flex;
    background-color: #cde2ff;
    padding: 5px;
    margin: 5px;
    line-height: 50px;
    height: 60px;
  }
  .candidate {
    width: 60px;
    color: #fff;
    font-size: 14px;
  }
  .candidate img {
    width: 100%;
  }
  .votes {
    font-size: 24px;
    flex-grow: 1;
    text-align: right;
    padding: 5px;
  }
  .precentage {
    font-size: 12px;
    font-size: 15px;
    margin-left: 15px;
    padding: 5px;
    background-color: #fff;
    width: 60px;
  }
</style>
