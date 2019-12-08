<template>
  <div class="home">
    <div class="dates">
      Dates
      <div v-for="item in filterDates" :key="item.id">{{ item.formattedDate }}</div>
    </div>
    <div class="cars">
      Volume of cars
      <div v-for="item in totalVolumeOfCars" :key="item.id">{{ item }}</div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import _ from "lodash";
import { format } from "date-fns";

export default {
  name: "Home",
  data() {
    return {
      yarraData: null
    };
  },
  computed: {
    // Formate Date 
    convertDate() {
      let newDateFormat = [];
      _.forEach(this.yarraData, num => {
        let newDate = num.date_captured;
        let formattedDate = format(new Date(newDate), "yyyy-MM");

        let vol = Number(num.volume_per_day);
        newDateFormat.push({ formattedDate, vol });
      });
      return newDateFormat;
    },
    // Sort Date
    sortData() {
      let sortedByDate = _.chain(this.convertDate)
        .sortBy("formattedDate")
        .groupBy("formattedDate")
        .value();
      return sortedByDate;
    },
    // Sum volume of cars for each day
    totalVolumeOfCars() {
      let totalDayVol = [];
      _.forEach(this.sortData, num => {
        let dayVol = [];
        _.forEach(num, num2 => {
          let vol = Number(num2.vol);
          dayVol.push(vol);
        });
        const dayVolReduce = dayVol.reduce((total, num) => {
          return total + num;
        });
        totalDayVol.push(dayVolReduce);
      });

      return totalDayVol;
    },    
    // Strip duplicate dates and sort separatly
    filterDates() {
      let filterDates = 
      _.chain(this.convertDate)
      .uniqBy( "formattedDate")    
      .sortBy("formattedDate").value()
      return filterDates
    }
  },

  beforeMount() {
    this.convertDate;
  },
  mounted() {
    axios
      .get(
        "https://data.gov.au/data/api/3/action/datastore_search_sql?sql=SELECT%20*%20from%20%229e26683b-6b30-424e-ace7-59047d811d1c%22"
      )
      .then(response => (this.yarraData = response.data.result.records));
  }
};
</script>

<style scoped lang="scss">
.home {
  display: flex;
}

.dates {
  margin-right: 10px;
}
</style>
