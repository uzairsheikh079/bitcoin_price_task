<template>
  <div id="app">
    <div class="vtc-downloads">
        <div class="vtc-downloads-controls">
        <div class="vtc-downloads-control">
            <!-- <strong>Select date range:</strong>
            <select v-model="selectedDateRange">
            <option
                v-for="(range, i) in dateRanges"
                :key="i"
                :value="range"
            >{{ range.from }} - {{ range.to }}</option>
            </select> -->
            <form>
              <strong>From</strong>
              <input type="date" v-model="dateRanges.from" required />
              <strong>To</strong>
              <input type="date" v-model="dateRanges.to" required />
              <input type="submit" value="Render" @click="getData()" />
            </form>
        </div>
        <div class="vtc-downloads-control">
            <!-- <strong>Date format:</strong>
            <select v-model="dateFormat">
            <option value="MM/DD">MM/DD</option>
            <option value="MM/DD/YYYY">MM/DD/YYYY</option>
            </select> -->
        </div>
        </div>
        <trend-chart v-if="dataset" :datasets="[{data: dataset}]" :labels="{
            xLabels: xLabels,
            yLabels: 3
        }" :min="0" :grid="{
            verticalLines: true,
            verticalLinesNumber: 1,
            horizontalLines: true,
            horizontalLinesNumber: 1
        }" />
    </div>
    </div>
</template>

<script>
import Vue from "vue";
import TrendChart from "vue-trend-chart";
import moment from 'moment';

Vue.use(TrendChart);

export default {
    el: "#app",
    data() {
      return{
        from:'',
        to:'',
        downloads: null,
        dateRanges: [
          {
          from: "2021-02-01",
          to: "2021-02-05"
          }
        ],
        selectedDateRange: null,
        dateFormat: "MM/DD"
      }
    },
    computed: {
      dataset() {
        return this.downloads && this.downloads.length
            ? this.downloads.map(d => d.downloads)
            : null;
      },
      xLabels() {
        return this.downloads && this.downloads.length
            ? this.downloads.map(d => moment(d.day).format(this.dateFormat))
            : [];
      }
    },
    methods: {
        onRangeChange() {
        this.fetchData();
        },
        fetchData() {
        this.axios.get(`https://api.npmjs.org/downloads/range/${this.selectedDateRange.from}:${this.selectedDateRange.to}/vue-trend-chart`)
            .then(res => {
            const { downloads } = res.data;
            this.downloads = downloads;
            })
            .catch(error => {
            console.log(error);
            });
        }
    },
    watch: {
        selectedDateRange() {
        this.fetchData();
        }
    },
    mounted() {
        this.selectedDateRange = this.dateRanges[0];
        this.fetchData();
    }

}
</script>

<style lang="scss" scoped>
* {
  box-sizing: border-box;
}

body {
  padding: 0;
  margin: 0;
  font-family: "Source Sans Pro", sans-serif;
  color: #2f4053;
}

#app {
  margin: 0 auto;
  padding: 20px;
  max-width: 800px;
}

.vtc-downloads {
  &-controls {
    @media (min-width: 699px) {
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    select {
      margin-left: 10px;
      font-size: 14px;
      border: 1px solid rgba(#000, 0.2);
      background: transparent;
    }
  }
  &-control {
    display: flex;
    align-items: center;
    margin-bottom: 20px;
    @media (max-width: 698px) {
      justify-content: space-between;
    }
  }

  .vtc {
    height: 250px;
    font-size: 12px;
    .stroke {
      stroke: #39af77;
      stroke-width: 2;
    }
  }
}
</style>