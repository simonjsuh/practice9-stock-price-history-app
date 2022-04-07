<template>
  <div class="lineChart">
    <div class="chartContainer">
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange()">1 day</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange()">1 week</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange()">1 month</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange()">1 year</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange()">5 years</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange(0)">MAX</button>
      <canvas id="myChart" width="400px" height="400px"></canvas>
    </div>
  </div>
</template>

<script>
import Chart from 'chart.js/auto';
import axios from 'axios'

let stockMarketHistory = [];
let stockMarketHistoryDates = [];
let stockMarketHistoryPrices = [];

let updateStockPriceHistoryChart = () => {
  // beginning of line chart code //
  const ctx = document.getElementById('myChart');

  // const labels = ['January', 'February', 'March', 'April', 'May', 'June', 'July'];
  const labels = stockMarketHistoryDates;
  const data = {
    labels: labels,
    datasets: [{
      label: 'My First Dataset',
      // data: [65, 59, 80, 81, 56, 55, 40],
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

  const myChart = new Chart(ctx, {
    type: 'line',
    data: data,
  });

  myChart;
  // end of line chart code //
}

export default {
  name: 'LineChart',
  mounted() {
    // beginning of stock API code
    let stockSymbol = 'IBM';

    let AlphaVantangeAPI_URL_Link = `https://www.alphavantage.co/query?function=TIME_SERIES_MONTHLY_ADJUSTED&symbol=${stockSymbol}&apikey=demo`;

    // let stockDataMonthlyAdjustedClosing = [];

    // let stockMarketHistory = [];
    // let stockMarketHistoryDates = [];
    // let stockMarketHistoryPrices = [];

    let createStockPriceHistoryChartInSpecifiedDateHistoryRange = (dateRange) => {
      axios.get(AlphaVantangeAPI_URL_Link)
        .then(response => {
          console.log(response);
          stockMarketHistory = response

          for (const property in stockMarketHistory.data["Monthly Adjusted Time Series"]) {
            // console.log(`${property}: ${stockMarketHistory.data["Monthly Adjusted Time Series"][property]["4. close"]}`);
            // console.log(`${stockMarketHistory.data["Monthly Adjusted Time Series"][property]["4. close"]}`);

            // let stockDataMonthlyAdjustedClosingDateEpoch = new Date();
            // console.log(property);

            let closingPrice = stockMarketHistory.data["Monthly Adjusted Time Series"][property]["4. close"];

            let closingDateMonth = property.split('-')[1];
            let closingDateYear = property.split('-')[0];
            let closingDateDay = property.split('-')[2];
            let closingDateProperlyFormatted = `${closingDateMonth}/${closingDateDay}/${closingDateYear}`;
            // let closingDateEpochTime = Date.parse(closingDateProperlyFormatted);

            // console.log(closingDateEpochTime);
            // console.log(closingPrice);

            // if (closingDateProperlyFormatted > dateRange) {
            //   stockMarketHistoryDates.unshift(closingDateProperlyFormatted)
            //   stockMarketHistoryPrices.unshift(closingPrice)
            // }

          dateRange;

            stockMarketHistoryDates.unshift(closingDateProperlyFormatted)
            stockMarketHistoryPrices.unshift(closingPrice)
          }

          console.log(stockMarketHistory)
        })
        .then(() => {
          // console.log(stockMarketHistory.data["Monthly Adjusted Time Series"]["2022-03-31"]["4. close"]);

          // console.log(stockMarketHistoryDates.slice(-3));
          // console.log(stockMarketHistoryPrices.slice(-3));

          updateStockPriceHistoryChart();
        });
    }
    // end of stock API code

    createStockPriceHistoryChartInSpecifiedDateHistoryRange(0);

    return {
      createStockPriceHistoryChartInSpecifiedDateHistoryRange,
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.chartContainer {
  width: 1000px;
  height: 300px;
}
</style>
