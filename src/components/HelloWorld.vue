/* eslint-disable no-console */
<template>
  <div class="hello">
    <ul>
      <li v-for="(item, index) in list" :key="index" @click="test(index)">
        {{ item.id }}
      </li>
    </ul>
    <el-dialog title="收货地址" :visible.sync="dialogTableVisible">
      <p>{{ currentData }}</p>
    </el-dialog>
    <el-pagination
      background
      :page-size="pagesize"
      :pager-count="11"
      layout="prev, pager, next"
      @current-change="handlechange"
      :total="100"
    >
    </el-pagination>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      pagesize:6,
      dialogTableVisible: false,
      list: [
        { id: 0, text: "1" },
        { id: 1, text: "2" },
        { id: 2, text: "3" },
        { id: 3, text: "4" },
        { id: 4, text: "5" },
        { id: 5, text: "6" }
      ],
      database: [
        [
          { id: 0, text: "1" },
          { id: 1, text: "2" },
          { id: 2, text: "3" },
          { id: 3, text: "4" },
          { id: 4, text: "5" },
          { id: 5, text: "6" }
        ],
        [
          { id: 8, text: "1" },
          { id: 9, text: "2" },
          { id: 54, text: "3" },
          { id: 133, text: "4" },
          { id: 444, text: "5" },
          { id: 555, text: "6" }
        ],
        [
          { id: 321, text: "1" },
          { id: 123, text: "2" },
          { id: 53454, text: "3" },
          { id: 634, text: "4" },
          { id: 345, text: "5" },
          { id: 678, text: "6" }
        ]
      ],
      currentData: {},
      currentIndex: 0
    };
  },
  props: {
    msg: String
  },
  watch: {
    dialogTableVisible(nv) {
      // eslint-disable-next-line no-console
      console.log(nv);
      if (nv) {
        document.addEventListener("keydown", this.keydown);
      } else {
        document.removeEventListener("keydown", this.keydown);
      }
    }
  },
  methods: {
    keydown(e) {
      // eslint-disable-next-line no-console
      switch (e.keyCode) {
        case 37:
          this.handleIndex('down')
          break;
        case 39:
          this.handleIndex('up')
          break;
        default:
          break;
      }
    },
    test(i) {
      // eslint-disable-next-line no-console
      console.log(i);
      this.getitem()
      this.currentIndex = i;
      this.dialogTableVisible = true;
    },
    handlechange(val) {
      // eslint-disable-next-line no-console
      console.log(val);
      setTimeout(() => {
        this.list = this.database[val - 1];
      }, 200);
    },
    getitem() {
      // this.currentData = this.list[this.currentIndex];
    },
    handleIndex(flag){
      
      if(flag==='up'){
        ++this.currentIndex
        if(this.currentIndex>18){
          this.currentIndex=18
          alert('没数据了亲')
          return
        }
        console.log('up')
        console.log(this.currentIndex)
        if(this.currentIndex!==1&&this.currentIndex%this.pagesize===1){
          let pagenum = Math.floor(this.currentIndex / this.pagesize)+1
          if(pagenum===0){
            pagenum=1
          }
            console.log('翻页',pagenum)
        }
        this.getitem()
        console.log(this.currentIndex)
      }
      if(flag==='down'){
        console.log('down')
        --this.currentIndex
        if(this.currentIndex<1){
          this.currentIndex=1
          alert('没数据了亲')
          return
        }
        if(this.currentIndex%this.pagesize===0){
            let pagenum = Math.floor(this.currentIndex / this.pagesize)
            if(pagenum===0){
              pagenum=1
            }
            console.log('翻页',pagenum)
        }
        this.getitem()
        console.log(this.currentIndex)

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
</style>
