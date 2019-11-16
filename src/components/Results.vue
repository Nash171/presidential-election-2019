<template>
  <div class="resluts-container">
    <div class="result" v-for="result in results.filter(d=>selectedDistrict==0||selectedDistrict==d.districtId)" :key="result.id">
      <div class="division">
        <span class="districtName">{{ districts[result.districtId].name }}</span> district - <span class="divisionName">{{ result.divisionName }}</span>
      </div>
      <div class="record-container">
        <div class="record" v-for="record in result.count" :key="record.id">
          <div class="candidate">
            {{ record.candidate }}
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
  </div>
</template>

<script>
  export default {
    name: 'Results',
    props: {
      results: Array,
      districts: Array,
      selectedDistrict: Number
    },
  }
</script>

<style scoped>
  .resluts-container {
    display: flex;
    align-self: center;
    max-width: 600px;
    width: 100%;
    flex-direction: column;
  }
  .result {
    margin-top: 10px;

  }
  .division {
    background-color: #35309c;
    color: #FFF;
    padding: 10px;
    font-size: 12px;
    font-weight: bold;
  }
  .districtName {
    font-size: 17px;
  }
  .divisionName {
    font-size: 17px;
  }

  .record-container {
    padding: 10px;
    background-color: #dcdcdc;
  }
  .record {
    display: flex;
    background-color: #cde2ff;
    padding: 2px;
    margin: 5px;
    line-height: 20px;
    height: 30px;
  }
  .candidate {
    width: 75%;
    padding: 5px;
    background-color: #438bef;
    color: #fff;
    font-size: 14px;
  }
  .votes {
    width: 50%;
    text-align: right;
    font-size: 14px;
    padding: 5px;
  }
  .precentage {
    font-size: 12px;
    margin-left: 15px;
    background-color: #f1f7ff;
    padding: 5px;
    width: 80px;
  }
</style>
