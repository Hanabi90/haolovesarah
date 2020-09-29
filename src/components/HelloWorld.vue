/* eslint-disable no-console */
<template>
  <div class="hello">
    <button @click="tab = !tab">点击切换</button>
    <div id="top" v-if="tab">
      <input type="file" id="file" accept=".txt" @change="uploadfile" />
    </div>
    <div v-else>
      <textarea
        v-model="str"
        ref="text"
        @blur="fn"
        name=""
        id="text"
      ></textarea>
      <button @click="palyall">播放整句</button>
      <button @click="tabvoice">切换声音</button>
      <button @click="sendData">发送请求</button>
    </div>
    <div id="result"></div>
    <template v-for="(item, index) in hanzi">
      <ruby :key="index" @click="read(item)"
        >{{ item }}<rt>{{ pinyin[index] }}</rt></ruby
      >
    </template>
    <!-- <iframe ref="iframe" id="iframe" src="https://www.biquwo.com/bqw69492/3675642.html" frameborder="0"></iframe> -->
    <p><a href="#top">回到顶部</a></p>
  </div>
</template>

<script>
import axios from "axios";

import { btts, ttt } from "../baidu";

var pinyin = require("pinyin");
var han = /^[\u4e00-\u9fa5]+$/;
function isChinese(temp) {
  if (han.test(temp)) {
    return true;
  }
}

export default {
  name: "HelloWorld",
  data() {
    return {
      hanzi: [],
      pinyin: [],
      str: "宝贝是个小流氓",
      tab: false,
      text: "我",
      per: [106, 110, 111, 103, 5, 1, 0, 3, 4],
      index: 0,
      voice: 0,
      postText: ""
    };
  },
  props: {
    msg: String
  },
  mounted() {
    axios.post('https://aip.baidubce.com/oauth/2.0/token',{
      grant_type:'client_credentials',
      client_id:'B24nezUbGPP5rVzG6ThQ8LO1',
      client_secret:'Fd94p0Ws0SMghL3owXmz4HxeOFKkqfxS'
    }).then((res)=>{
      console.log(res)
    })
    //dom加载完毕回调后注册按钮对象

    //  this.bttts()
    //      axios.post('https://aip.baidubce.com/rpc/2.0/nlp/v1/lexer?charset=UTF-8&access_token=24.b277fbcd3b5e17d6ecad50d687b21d3d.2592000.1578838446.282335-18017265',).then((res)=>{
    //   console.log(res)
    // })
    this.ready(() => {
      this.playBtn = document.getElementById("playBtn");
    });

    // 合成按钮

    // dom加载完毕回调
  },
  methods: {
    sendData() {
      let params = {
        text: this.str
      };
      axios.post("https://usw1.kubesail.com/", params).then(res => {
        console.log(res);
      });
    },
    tabvoice() {
      this.index++;
      this.index = this.index % this.per.length;
      this.voice = this.per[this.index];
    },
    palyall() {
      this.text = this.str;
      this.tts();
    },
    read(item) {
      this.text = item;
      this.tts();
    },
    ready(callback) {
      var doc = document;
      if (doc.addEventListener) {
        doc.addEventListener(
          "DOMContentLoaded",
          function() {
            callback();
          },
          false
        );
      } else if (doc.attachEvent) {
        doc.attachEvent("onreadystatechange", function() {
          if (doc.readyState === "complete") {
            callback();
          }
        });
      }
    },
    bttts() {
      // https://aip.baidubce.com/rpc/2.0/nlp/v1/lexer?charset=UTF-8&access_token=24.b277fbcd3b5e17d6ecad50d687b21d3d.2592000.1578838446.282335-18017265
      ttt({
        charset: "UTF-8",
        access_token:
          "24.23bfab29758df833b59e95c9aab202b4.2592000.1603988305.282335-15670276",
        text: "百度是一家高科技公司"
      });
    },
    tts() {
      // 调用语音合成接口
      // 参数含义请参考 https://ai.baidu.com/docs#/TTS-API/41ac79a6
      // eslint-disable-next-line no-unused-vars
      var that = this;
      this.audio = btts(
        {
          tex: this.text,
          tok:
            "24.23bfab29758df833b59e95c9aab202b4.2592000.1603988305.282335-15670276",
          spd: 3,
          pit: 5,
          vol: 15,
          per: this.voice
        },
        {
          volume: 0.3,
          autoDestory: true,
          timeout: 10000,
          hidden: true,
          onSuccess: htmlAudioElement => {
            // console.log(htmlAudioElement)
            that.audio = htmlAudioElement;
            this.audio.play();
          },
          // onError: (text)=> {
          //     // console.log(text)
          // },
          onTimeout: () => {
            // console.log('timeout')
          }
        }
      );
    },
    // 暂停按钮
    pause() {
      if (this.audio === null) {
        console.log("请先点击合成");
      } else {
        this.audio.pause();
      }
    },

    // 取消按钮
    cancel() {
      if (this.audio === null) {
        alert("请先点击合成");
      } else {
        this.audio.pause();
        document.body.removeChild(this.audio);
        this.audio = null;
        this.playBtn.innerText = "准备中";
      }
    },
    check() {
      console.log(this.$refs.iframe.document.getElementById("content"));
    },
    uploadfile() {
      let reads = new FileReader();
      var file = document.getElementById("file").files[0];
      reads.readAsText(file, "utf-8");
      // console.log(reads);
      var that = this;
      reads.onload = function(e) {
        // document.getElementById('result').innerText = e.target.result
        that.str = e.target.result;
        that.fn();
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
    fn() {
      // var arr =[]
      var str = this.str;
      // arr = Array.from(str)
      str = str.replace(/\s*/g, "");
      var arr2 = [];
      var arr3 = [];
      for (let index = 0; index < str.length; index++) {
        const item = str[index];
        if (isChinese(item)) {
          arr2.push(item);
        } else {
          arr3.push({ index, item });
        }
      }
      arr2 = arr2.join("");
      // console.log(arr3)
      this.hanzi = Array.from(arr2);
      this.pinyin = pinyin(arr2);
      // console.log(this.hanzi)
      for (let i = 0; i < arr3.length; i++) {
        const item = arr3[i];
        this.hanzi.splice(item.index, 0, item.item);
        this.pinyin.splice(item.index, 0, item.item);
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
rt {
  color: red;
}
ruby {
  margin: 2px;
  line-height: 60px;
  cursor: pointer;
}
</style>
