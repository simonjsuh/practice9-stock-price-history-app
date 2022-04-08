<template>
  <div class="lineChart">
    <div class="chartAndButtonsContainer">
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRangeSETUP(86400)">1 day</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRangeSETUP(604800)">1 week</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRangeSETUP(2630000)">1 month</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRangeSETUP(31536000)">1 year</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRangeSETUP(157680000 )">5 years</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRangeSETUP(10000000000000000)">MAX</button>

      <div id="chartContainer">
        <canvas id="myChart" width="400px" height="400px"></canvas>
      </div>
    </div>
  </div>
</template>

<script>
import Chart from 'chart.js/auto';
import axios from 'axios'

let stockMarketHistory = [];
let stockMarketHistoryDates = [];
let stockMarketHistoryEpochDates = [];
let stockMarketHistoryPrices = [];

let myChart;
myChart;

export default {
  name: 'LineChart',
  mounted() {
    // beginning of stock API code
    let stockSymbol = 'IBM';

    let AlphaVantangeAPI_URL_Link = `https://www.alphavantage.co/query?function=TIME_SERIES_MONTHLY_ADJUSTED&symbol=${stockSymbol}&apikey=demo`;

    let createStockPriceHistoryChartInSpecifiedDateHistoryRange = (dateRange) => {
      axios.get(AlphaVantangeAPI_URL_Link)
        .then(response => {
          stockMarketHistory = response

          for (const property in stockMarketHistory.data["Monthly Adjusted Time Series"]) {
            let closingPrice = stockMarketHistory.data["Monthly Adjusted Time Series"][property]["4. close"];
            let closingDateMonth = property.split('-')[1];
            let closingDateYear = property.split('-')[0];
            let closingDateDay = property.split('-')[2];
            let closingDateProperlyFormatted = `${closingDateMonth}/${closingDateDay}/${closingDateYear}`;
            let closingDateEpochTime = Date.parse(closingDateProperlyFormatted)/1000;

            dateRange;

            closingDateProperlyFormatted
            closingPrice
            closingDateEpochTime

            stockMarketHistoryDates.unshift(closingDateProperlyFormatted)
            stockMarketHistoryEpochDates.unshift(closingDateEpochTime)
            stockMarketHistoryPrices.unshift(closingPrice)
          }
        })
        .then(() => {
          let now = Date.now()/1000;
          let dateRangeDate = now - dateRange;

          const isLaterDateThanDateRangeDate = (date) => {
            return date > dateRangeDate;
          }
          
          let arrayIndexWhereDateIsLargerThanDateRangeDate = stockMarketHistoryEpochDates.findIndex(isLaterDateThanDateRangeDate);

          stockMarketHistoryDates.splice(0, arrayIndexWhereDateIsLargerThanDateRangeDate);
          stockMarketHistoryPrices.splice(0, arrayIndexWhereDateIsLargerThanDateRangeDate);

          this.updateStockPriceHistoryChart();
        });
    }
    // end of stock API code

    createStockPriceHistoryChartInSpecifiedDateHistoryRange(23456345634563463465436);
  },
  setup() {
    let updateStockPriceHistoryChart = () => {
      // beginning of line chart code //
      const ctx = document.getElementById('myChart');

      const labels = stockMarketHistoryDates;
      const data = {
        labels: labels,
        datasets: [{
          label: 'My First Dataset',
          data: stockMarketHistoryPrices,
          fill: false,
          borderColor: 'rgb(75, 192, 192)',
          tension: 0,
          options: {
            responsive:true,
            maintainAspectRatio: false,
          }
        }]
      };

      let myChart = null;
  
      const chartWithKey = Chart.getChart('myChart');
      if (chartWithKey != undefined) {
        chartWithKey.destroy();
      }

      myChart = new Chart(ctx, {
        type: 'line',
        data: data,
      });

      myChart;
    }


    // beginning of stock API code
    let stockSymbol = 'IBM';

    let AlphaVantangeAPI_URL_Link = `https://www.alphavantage.co/query?function=TIME_SERIES_MONTHLY_ADJUSTED&symbol=${stockSymbol}&apikey=demo`;

    let createStockPriceHistoryChartInSpecifiedDateHistoryRangeSETUP = (dateRange) => {
      // empty array if something already in there
      if (stockMarketHistoryDates.length > 0) {
        stockMarketHistoryDates = [];
        stockMarketHistoryEpochDates = [];
        stockMarketHistoryPrices = [];
      }

      axios.get(AlphaVantangeAPI_URL_Link)
        .then(response => {
          stockMarketHistory = response

          for (const property in stockMarketHistory.data["Monthly Adjusted Time Series"]) {
            let closingPrice = stockMarketHistory.data["Monthly Adjusted Time Series"][property]["4. close"];

            let closingDateMonth = property.split('-')[1];
            let closingDateYear = property.split('-')[0];
            let closingDateDay = property.split('-')[2];
            let closingDateProperlyFormatted = `${closingDateMonth}/${closingDateDay}/${closingDateYear}`;
            let closingDateEpochTime = Date.parse(closingDateProperlyFormatted)/1000;

            dateRange;

            closingDateProperlyFormatted
            closingPrice
            closingDateEpochTime

            stockMarketHistoryDates.unshift(closingDateProperlyFormatted)
            stockMarketHistoryEpochDates.unshift(closingDateEpochTime)
            stockMarketHistoryPrices.unshift(closingPrice)
          }
        })
        .then(() => {
          console.log(dateRange);
          let now = Date.now()/1000;
          console.log('Now: ' + now + '\n5 years ago: ' + (now - dateRange));
          let dateRangeDate = now - dateRange;
          console.log('dateRangeDate: ' + dateRangeDate);

          const isLaterDateThanDateRangeDate = (date) => {
            return date > dateRangeDate;
          }

          let arrayIndexWhereDateIsLargerThanDateRangeDate = stockMarketHistoryEpochDates.findIndex(isLaterDateThanDateRangeDate);

          console.log(arrayIndexWhereDateIsLargerThanDateRangeDate);

          stockMarketHistoryDates.splice(0, arrayIndexWhereDateIsLargerThanDateRangeDate);
          stockMarketHistoryPrices.splice(0, arrayIndexWhereDateIsLargerThanDateRangeDate);

          console.log(stockMarketHistoryDates);

          updateStockPriceHistoryChart();

          console.log('In setup');
          console.log('In setup: ' + myChart);
          console.log(window.myChart);
        });
    }
    // end of stock API code

    return {
      createStockPriceHistoryChartInSpecifiedDateHistoryRangeSETUP,
      updateStockPriceHistoryChart,
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#chartContainer {
  width: 1000px;
  height: 300px;
}
</style>