/* eslint-disable no-console */
<template>
  <div class="hello">
   <input type="file" id="file" accept=".txt" @change=uploadfile />
   <div id="result"> </div>
      <template v-for="(item,index) in hanzi">
        <ruby :key="index">{{item}}<rt>{{pinyin[index]}}</rt></ruby>
      </template>
  </div>
</template>

<script>
var pinyin = require("pinyin");
 var han = /^[\u4e00-\u9fa5]+$/;
function isChinese(temp){
  if (han.test(temp)){
     return true
  }
}
export default {
  name: "HelloWorld",
  data() {
    return {
      hanzi:[],
      pinyin:[],
      str:''
    };
  },
  props: {
    msg: String
  },
  mounted(){
    // this.fn()
  },
  methods: {
    uploadfile() {
      let reads = new FileReader();
      var file = document.getElementById('file').files[0];
      reads.readAsText(file, 'utf-8');
      console.log(reads);
      var that = this
      reads.onload = function (e) {
          // document.getElementById('result').innerText = e.target.result
          that.str = e.target.result
          that.fn()
      };
      // reads.onloadstart = function(e) {
      //     console.log('onloadstart ---> ', e)
      // }
      // reads.onloadend = function(e) {
      //     console.log('onloadend ---> ', e)
      // }
      // reads.onprogress = function(e) {
      //     console.log('onprogress ---> ', e)
      // }
    },
    fn(){
      // var arr =[]
      var str = this.str
      // arr = Array.from(str)
      str = str.replace(/\s*/g,"");
      var arr2 = []
      var arr3 = []
      for (let index = 0; index < str.length; index++) {
        const item = str[index];
        if(isChinese(item)){
          arr2.push(item)
        }else{
          arr3.push({index,item})
        }
      }
      arr2 = arr2.join('')
      // console.log(arr3)
      this.hanzi = Array.from(arr2)
      this.pinyin = pinyin(arr2)
      // console.log(this.hanzi)
      for (let i = 0; i < arr3.length; i++) {
        const item = arr3[i]
        this.hanzi.splice(item.index,0,item.item)
        this.pinyin.splice(item.index,0,item.item)
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
li {
  margin: 10px;
  padding: 4px;
  border: 1px solid;
  cursor: pointer;
}
rt{
  color: red;
}
ruby{
  margin:2px;
  line-height: 60px;
}
</style>
