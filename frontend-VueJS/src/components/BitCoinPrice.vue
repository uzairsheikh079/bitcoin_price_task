<template>
  <div>
    <div>
      <h2>{{ msg }}</h2>
    </div>
    <div>
      <form>
        <strong>From</strong>
        <input type="date" v-model="from" required />

        <strong>To</strong>
        <input type="date" v-model="to" required />

        <input type="button" value="Render" @click="getData()" />
      </form>
    </div>
    <!-- <div>
      <TrendChart :datasets="datasets" :grid="grid" :labels="label" :min="0">
      </TrendChart>
    </div> -->
    <div class="bitcoin-price">
      <svg
        style="width:0; height:0; position:absolute;"
        aria-hidden="true"
        focusable="false"
      >
        <defs>
          <linearGradient id="btcFill" x1="1" x2="1" y1="0" y2="1">
            <stop offset="0%" stop-color="#f69119"></stop>
            <stop offset="100%" stop-color="#ffffff"></stop>
          </linearGradient>
        </defs>
      </svg>
      <trend-chart
        v-if="dataset.length"
        :datasets="[{ data: dataset, fill: true, className: 'curve-btc' }]"
        :labels="labels"
        :min="0"
        :grid="grid"
      />
    </div>
    <div v-if="disclaimer">
        <p>{{disclaimer}}</p>
    </div>
  </div>
</template>

<script>
import Vue from "vue";
import TrendChart from "vue-trend-chart";
import moment from 'moment';

Vue.use(TrendChart);

export default {
  name: "BitCoinPrice",
  props: { msg: String },
  data() {
    return {
        from: "",
        to: "",
        dataset: [],
        labels: {
          xLabels: [],
          yLabels: 5,
        yLabelsTextFormatter: val => "$" + Math.round(val * 100) / 100
        },
        grid: {
          verticalLines: true,
          verticalLinesNumber: 1,
          horizontalLines: true,
          horizontalLinesNumber: 1
        },
        disclaimer:''
    };
  },
  mounted() {
    this.from =  moment().subtract(10, 'days').format('YYYY-MM-DD');
    this.to = moment().format('YYYY-MM-DD');
  },
  methods: {
    getData(){
        this.dataset= [];
        this.labels= {
          xLabels: [],
          yLabels: 5,
          yLabelsTextFormatter: val => "$" + Math.round(val * 100) / 100
        };
        // let url = `https://api.coindesk.com/v1/bpi/historical/close.json?start=`+this.from+`&end=`+this.to;
        let url = `http://localhost/Bitcoin-price/Backend-laravel/public/bitCoinRates?start=`+this.from+`&end=`+this.to;
        this.axios.get(url)
        .then(res => {
            const data = res.data.bpi;
            const disclaimer = res.data.disclaimer;
            for (let key in data) {
            this.disclaimer = disclaimer;
            this.dataset.push(data[key]);
            this.labels.xLabels.push(moment(key).format("YYYY-MM-DD"));
            }
        });
    }
  }
};
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
  max-width: 600px;
}
.bitcoin-price {
  .vtc {
    height: 250px;
    font-size: 12px;
    @media (min-width: 699px) {
      height: 350px;
    }
  }
  .grid,
  .labels {
    line {
      stroke: rgba(#f69119, 0.5);
    }
  }
  .x-labels {
    .label {
      text {
        display: none;
      }
      line {
        opacity: 0.3;
      }
      &:nth-child(6n + 1),
      &:first-child {
        text {
          display: block;
        }
        line {
          opacity: 1;
        }
      }
    }
  }
  .curve-btc {
    .stroke {
      stroke: #f69119;
      stroke-width: 2;
    }
    .fill {
      fill: url(#btcFill);
      fill-opacity: 0.5;
    }
  }
}
</style>
