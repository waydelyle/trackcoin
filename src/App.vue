<div id="app">
</div>

<script>
import { Line } from 'vue-chartjs'
const Client = require('coinbase').Client;

const client = new Client({
    'apiKey': 'kEKIEsnKiX1JUQxa',
    'apiSecret': 'D9DSn0JZrb3GbxO68kHsvQvKiBPfC8eK',
    'version':'YYYY-MM-DD',
    strictSSL: false,
});

const currencyCode = 'USD'; // can also use EUR, CAD, etc.

export default {
  name: 'app',
  extends: Line,
  mounted () {
      this.showChart();
      setInterval(this.showChart, 15000);
  },
  computed: {
      chartLabels: function() {
          return this.labels;
      },
      chartPrices: function() {
          return this.price;
      },
  },
  methods: {
      showChart() {
          let self = this;
          client.getSpotPrice({'currency': 'USD'}, function(err, price) {

              if (err !== null) {
                  console.log(err);
                  return;
              }

              self.labels.push([
                  new Date().toLocaleTimeString()
              ]);

              self.price.push([
                  price.data.amount
              ]);

              if (self.$data._chart) {
                  self.$data._chart.destroy();
              }

              if (self.price.length > 30) {
                  self.price.splice(0, 1);
                  self.labels.splice(0, 1);
              }

              self.renderChart(
                  {
                      labels: self.chartLabels,
                      datasets: [
                          {
                              label: 'Bitcoin spot price',
                              backgroundColor: '#ADD8E6',
                              data: self.chartPrices,
                          }
                      ],

                  },
                  {
                      responsive: true,
                      maintainAspectRatio: false
                  }
              );
          });
      }
  },
  data () {
    return {
      labels: [],
      price: [],
    }
  },
}
</script>

<style lang="scss">
@import '../node_modules/bootstrap/scss/bootstrap.scss';
</style>
