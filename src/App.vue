<template>
  <div id="hot-preview">
    <h1>たしざんつーる</h1>
    <HotTable :root="root" :settings.sync="hotSettings"></HotTable>
    <div class="total">
      <div class="total-sum">
        合計： <input type="text" :value="total">
      </div>
      <div class="total-num">
        行数： <input type="text" :value="ary.length">
      </div>
      <div class="total-average">
        平均： <input type="text" :value="average">
      </div>
    </div>
    <div class="howto">
      使い方<br><br> 入力した行の合計が表示されます。それだけです。 一行しかないExcelみたいに使ってください。
    </div>
  </div>
</template>

<script>
  import Handsontable from 'handsontable';
  import HotTable from 'vue-handsontable-official';
  import Vue from 'vue';
  let initialDataSet = []
  for (let i = 0; i < 256; i++) {
    initialDataSet.push([""])
  }

  function checkArray(ary){
    var isError = false
    ary.forEach((element) => {
      if (parseFloat(element) === NaN) {
        isError = true
      }
    });

    if (isError) {
      return NaN
    }
  }

  export default {
    data: function () {
      return {
        root: 'test-hot',
        dataAry: [],
        hotSettings: {
          data: initialDataSet,
          width: 250,
          height: 320,
          colWidths: 200,
          rowHeights: 23,
          afterChange: (data) => {
            this.dataAry = this.hotSettings.data.reduce((a, b) => a.concat(b))
          }
        },
      };
    },
    computed: {
      ary: function(){
        return this.dataAry.filter(line => line.length > 0)
      },
      total: function () {
        if(checkArray(this.ary) === NaN){
          return "NaN"
        }
        if (this.ary.length === 0) {
          return "0"
        }

        return this.ary.reduce((a, b) => parseFloat(a) + parseFloat(b))
      },
      average: function(){
        return this.total / this.ary.length
      }
    },
    components: {
      HotTable
    },
    mounted() {
    }
  }

</script>

<style>
  #test-hot {
    width: 240px;
    overflow: hidden;
  }
  .total-sum{
    color: blue;
  }
  .total-num{
    color: #999;
  }
  .total-average{
    color: #999;
  }
  .howto{
    border: 1px solid;
    padding: 1rem;
    margin: 1rem 0;
  }
</style>