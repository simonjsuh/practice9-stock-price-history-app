<template>
  <div class="lineChart">
    <div class="chartAndButtonsContainer">
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange(86400)">1 day</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange(604800)">1 week</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange(2630000)">1 month</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange(31536000)">1 year</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange(157680000 )">5 years</button>
      <button @click="createStockPriceHistoryChartInSpecifiedDateHistoryRange(10000000000000000)">MAX</button>

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



// let updateChart = () => {
//   console.log(myChart)
// }

// let updateStockPriceHistoryChart_IfChartAlreadyExists = () => {
//   myChart.destroy();
//   updateStockPriceHistoryChart;
// }

// updateStockPriceHistoryChart_IfChartAlreadyExists;

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
          // console.log(response);
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
            let closingDateEpochTime = Date.parse(closingDateProperlyFormatted)/1000;

            // console.log(closingDateEpochTime);
            // console.log(closingPrice);

            // if (closingDateProperlyFormatted > dateRange) {
            //   stockMarketHistoryDates.unshift(closingDateProperlyFormatted)
            //   stockMarketHistoryPrices.unshift(closingPrice)
            // }

            dateRange;

            // stockMarketHistoryDates = [];
            // stockMarketHistoryPrices = [];

            closingDateProperlyFormatted
            closingPrice
            closingDateEpochTime

            stockMarketHistoryDates.unshift(closingDateProperlyFormatted)
            stockMarketHistoryEpochDates.unshift(closingDateEpochTime)
            stockMarketHistoryPrices.unshift(closingPrice)
          }

          // console.log(stockMarketHistory)
        })
        .then(() => {
          // console.log(stockMarketHistory.data["Monthly Adjusted Time Series"]["2022-03-31"]["4. close"]);
          
          // console.log(stockMarketHistoryDates.length);
          // console.log(stockMarketHistoryPrices.length);

          // stockMarketHistoryDates = [];
          // stockMarketHistoryPrices = [];

          // find index of stockMarketHistoryDates where the value is less than 5 years ago.
          
          // console.log(dateRange);
          let now = Date.now()/1000;
          // console.log('Now: ' + now + '\n5 years ago: ' + (now - dateRange));
          let dateRangeDate = now - dateRange;
          // console.log('dateRangeDate: ' + dateRangeDate);

          const isLaterDateThanDateRangeDate = (date) => {
            return date > dateRangeDate;
          }

          // console.log(stockMarketHistoryEpochDates);
          // console.log('dateRangeDate: ' + dateRangeDate);
          
          let arrayIndexWhereDateIsLargerThanDateRangeDate = stockMarketHistoryEpochDates.findIndex(isLaterDateThanDateRangeDate);

          // console.log(arrayIndexWhereDateIsLargerThanDateRangeDate);

          // update stockMarketHistoryDates and stockMarketHistoryPrices arrays to delete all values before the index indicated by arrayIndexWhereDateIsLargerThanDateRangeDate

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

      // let clearCanvas = ctx.getContext('2d');
      // clearCanvas.clearRect(0, 0, ctx.width, ctx.height);

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

      let myChart = null;
  

      // if (myChart != 'undefined') {
      //   console.log('before chart init');
      //   console.log(myChart);

      //   myChart.destroy();
      // }
      


      // if (myChart != null) {
      //   console.log(myChart);
      //   myChart.destroy();
      //   console.log('destroyed');
      // } else {
      //   console.log('nothing there to destroy');
      //   console.log(myChart);
      // }


      // if (myChart) {
      //   myChart.destroy();
      // }

      const chartWithKey = Chart.getChart('myChart');
      if (chartWithKey != undefined) {
        chartWithKey.destroy();
      }

      myChart = new Chart(ctx, {
        type: 'line',
        data: data,
      });

      console.log('working myChart: ' + myChart);
      console.log(myChart)
      // end of line chart code //
    }


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
          // console.log(response);
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
            let closingDateEpochTime = Date.parse(closingDateProperlyFormatted)/1000;

            // console.log(closingDateEpochTime);
            // console.log(closingPrice);

            // if (closingDateProperlyFormatted > dateRange) {
            //   stockMarketHistoryDates.unshift(closingDateProperlyFormatted)
            //   stockMarketHistoryPrices.unshift(closingPrice)
            // }

            dateRange;

            // stockMarketHistoryDates = [];
            // stockMarketHistoryPrices = [];

            closingDateProperlyFormatted
            closingPrice
            closingDateEpochTime

            stockMarketHistoryDates.unshift(closingDateProperlyFormatted)
            stockMarketHistoryEpochDates.unshift(closingDateEpochTime)
            stockMarketHistoryPrices.unshift(closingPrice)
          }

          // console.log(stockMarketHistory)
        })
        .then(() => {
          // console.log(stockMarketHistory.data["Monthly Adjusted Time Series"]["2022-03-31"]["4. close"]);
          
          console.log(stockMarketHistoryDates.length);
          console.log(stockMarketHistoryPrices.length);

          // stockMarketHistoryDates = [];
          // stockMarketHistoryPrices = [];

          // find index of stockMarketHistoryDates where the value is less than 5 years ago.
          
          console.log(dateRange);
          let now = Date.now()/1000;
          console.log('Now: ' + now + '\n5 years ago: ' + (now - dateRange));
          let dateRangeDate = now - dateRange;
          console.log('dateRangeDate: ' + dateRangeDate);

          const isLaterDateThanDateRangeDate = (date) => {
            return date > dateRangeDate;
          }

          // console.log(stockMarketHistoryEpochDates);
          // console.log('dateRangeDate: ' + dateRangeDate);
          
          let arrayIndexWhereDateIsLargerThanDateRangeDate = stockMarketHistoryEpochDates.findIndex(isLaterDateThanDateRangeDate);

          console.log(arrayIndexWhereDateIsLargerThanDateRangeDate);

          // update stockMarketHistoryDates and stockMarketHistoryPrices arrays to delete all values before the index indicated by arrayIndexWhereDateIsLargerThanDateRangeDate

          stockMarketHistoryDates.splice(0, arrayIndexWhereDateIsLargerThanDateRangeDate);
          stockMarketHistoryPrices.splice(0, arrayIndexWhereDateIsLargerThanDateRangeDate);

          updateStockPriceHistoryChart();

          // updateChart();
          console.log('In setup');
          console.log('In setup: ' + myChart);
          console.log(window.myChart);
          // window.myChart.destroy();
        });
    }
    // end of stock API code

    return {
      createStockPriceHistoryChartInSpecifiedDateHistoryRange,
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
