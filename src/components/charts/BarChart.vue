<script>
import { HorizontalBar } from 'vue-chartjs';
import ChartJsPluginDataLabels from 'chartjs-plugin-datalabels';
// eslint-disable-next-line no-unused-vars
import { mapGetters } from 'vuex';

export default {
  name: 'BarChart',
  components: {
    ChartJsPluginDataLabels,
  },
  extends: HorizontalBar,
  props: {
    wpm: {
      type: Number,
      default: null,
    },
  },
  data() {
    return {
      backgroundColors: ['#266eb7', '#a411f9', '#c957e0'],
    };
  },
  computed: {
    ...mapGetters(['databaseStats']),
    chartData() {
      const scores = [
        { name: 'player', value: this.wpm },
        { name: 'avg', value: this.databaseStats.avgWPM },
        { name: 'best', value: this.databaseStats.best },
      ];
      scores.sort((a, b) => a.value - b.value);

      const data = {};

      scores.forEach((score, index) => {
        data[score.name] = {};
        data[score.name].order = index;
        data[score.name].value = score.value;
      });

      if (Math.round(data.player.value) === data.avg.value || Math.round(data.player.value) === data.best.value) {
        data.barWidth = true;
      }

      return data;
    },
    options() {
      return {
        responsive: true,
        maintainAspectRatio: false,
        events: [''],
        legend: {
          labels: {
            fontColor: '#fff',
            fontSize: 13,
          },
        },
        scales: {
          xAxes: [{
            stacked: false,
            ticks: {
              fontColor: '#aaa',
              min: 0,
            },
          }],
          yAxes: [{
            barPercentage: 0.7,
            gridLines: {
              display: false,
            },
          }],
        },
        plugins: {
          datalabels: {
            color: '#fff',
            align: 'end',
            anchor: 'end',
            formatter(value) {
              return `${value} wpm`;
            },
          },
        },
      };
    },

    chartDatasets() {
      return {
        datasets: [
          {
            barPercentage: this.chartData.barWidth ? 0.75 : 0.9,
            label: 'You',
            backgroundColor: this.backgroundColors[this.chartData.player.order],
            data: [this.chartData.player.value],
            order: this.chartData.player.order,
          },
          {
            label: 'AVG',
            backgroundColor: this.backgroundColors[this.chartData.avg.order],
            data: [this.chartData.avg.value],
            order: this.chartData.avg.order,
          },
          {
            label: 'Best',
            backgroundColor: this.backgroundColors[this.chartData.best.order],
            data: [this.chartData.best.value],
            order: this.chartData.best.order,
          },
        ],
      };
    },
  },
  mounted() {
    this.renderChart(this.chartDatasets, this.options);
  },
  methods: {
    format(number, precision = 2, scaler = 0.001) {
      return Math.round(number * scaler * (10 ** precision)) / (10 ** precision);
    },
  },
};
</script>

<style>

</style>
