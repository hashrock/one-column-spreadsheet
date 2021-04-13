<template>
  <div id="hot-preview">
    <h3>One Line Spreadsheet</h3>
    <HotTable :root="root" :settings.sync="hotSettings"></HotTable>
    <div class="total">
      <div class="total-row total-sum">
        <div class="total-row__label">合計</div>
        <input class="sum" type="text" :value="total" /></div>
      <div class="total-row total-average">
        <div class="total-row__label">平均</div>
        <input type="text" :value="average" />
      </div>
      <div class="total-row total-num">
        <div class="total-row__label">行数</div>
        <input class="num" type="text" :value="ary.length" />
      </div>
    </div>
    <div class="howto">
      <h4>
        使い方
      </h4>
      <div>
        入力した行の合計が表示されます。一行しかないExcelみたいに使ってください。
      </div>
    </div>
  </div>
</template>

<script>
import HotTable from "@handsontable/vue";
var formula = require("formula");

let initialDataSet = [];
for (let i = 0; i < 256; i++) {
  initialDataSet.push([""]);
}

function checkArray(ary) {
  var isError = false;
  ary.forEach((element) => {
    if (parseFloat(element) === NaN) {
      isError = true;
    }
  });

  if (isError) {
    return NaN;
  }
}

export default {
  data: function () {
    return {
      root: "test-hot",
      dataAry: [],
      hotSettings: {
        data: initialDataSet,
        width: 300,
        height: 320,
        colWidths: 270,
        rowHeights: 23,
        className: "htRight",
        afterChange: (data) => {
          this.dataAry = this.hotSettings.data.reduce((a, b) => a.concat(b));
        },
        licenseKey: "non-commercial-and-evaluation",
      },
    };
  },
  computed: {
    ary() {
      return this.dataAry
        .filter((line) => line && line.length > 0)
        .map((row) => (formula.isnumber(row) ? row : formula.run(row)));
    },
    total() {
      if (checkArray(this.ary) === NaN) {
        return "NaN";
      }
      if (this.ary.length === 0) {
        return "0";
      }

      return this.ary.reduce((a, b) => parseFloat(a) + parseFloat(b));
    },
    average: function () {
      return this.total / this.ary.length;
    },
  },
  components: {
    HotTable,
  },
  mounted() {},
};
</script>

<style>
@import "~handsontable/dist/handsontable.full.css";
#hot-preview{
  border-top: 4px solid #597bbcdb;
  width: 300px;
  margin: 0 auto;
}
body{
  background-color: #eee;
  margin: 0;
}

h3{
  font-family: system-ui, -apple-system, /* Firefox supports this but not yet `system-ui` */ 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji';
  font-weight: 300;
  text-align: center;
}

input{
  padding: 0.75rem 0.5rem;
  text-align: right;
  flex:1;
  font-size: 1.2em;
  min-width: 0;
  border: 1px solid #ccc;
  border-radius: 0.2em;
}
input.sum{
  font-weight: 700;
}
input.num{
  background: #f5f5f5;
  border: none;
}

#test-hot {
  overflow: hidden;
}

.total-row {
  display: flex;
  align-items: center;
  margin-top: 0.5em;
}
.total-row__label{
  padding: 0.5em;
}

.total-sum {
  color: rgb(75, 137, 255);
}
.total-num {
  color: #999;
}
.total-average {
  color: #999;
}
h4{
  margin: 0.5em 0;
}
.howto {
  padding: 0.5em;
  margin-top: 1em;
  background: #f6f6f6;
  color: #666;
}
</style>