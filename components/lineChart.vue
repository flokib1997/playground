<template>
<div class="small">
<line-chart :chart-data="datacollection"></line-chart>
</div>
</template>

<script>
import LineChart from './line.js';
const gpxParser = require('gpxparser');
import run from '!raw-loader!../assets/run.gpx';

export default {

  components: {
    LineChart
  },
  data () {
    return {
      datacollection: null,
      parser: null,
      distanceArray:null,
      workingArray:[],
    }
  },
  mounted () {
    this.parser = new gpxParser(); //Create gpxParser Object
    this.parser.parse(run);
    this.distanceArray = this.parser.tracks[0].distance.cumul;
    for(let i=1;i<this.distanceArray.length;i++) {
        this.workingArray.push(this.distanceArray[i] - this.distanceArray[i-1]);
    }

    this.fillData(this.workingArray);
  },
  methods: {
   chunkAverage(arr, len) {
  var chunks = [];
  var i = 0;
  var n = arr.length;

  var chunk;

  while (i < n) {
    chunk = arr.slice(i, i += len);
    chunks.push(
      chunk.reduce((s, n) => s + n) / chunk.length
    );
  }

  return chunks;
},
    fillData (arr) {
     let tmp = this.chunkAverage(arr,50);
     console.log(arr.length);
     console.log(tmp.length);
      this.datacollection = {
        labels: [...tmp],
        datasets: [
          {
            label: 'Progress',
            backgroundColor: '#f87979',
            data: [...tmp]
          }
        ]
      }


    },
  }
}
</script>

<style>
.small {
  max-width: 600px;
  margin:  150px auto;
}
</style>
