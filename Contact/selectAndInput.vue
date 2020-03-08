<template>
  <div id="selectAndInput">
    <div class="placeholder"></div>
    <div class="selectAndInputInner">
      <div class="inputWrap">
        <div class="selectPrefix" @click="isShow = !isShow">
          <img :src="selected.image" />
          <span>+{{selected.code}}</span>
        </div>
        <input type="text" class="prefixInput" :class="{error:isError}" v-model="inputData" :placeholder="placeholder"
         @blur="check"/>
      </div>
      <input type="text" class="search" v-model="filter" ref="search" v-show="isShow">
      <div class="optionsWrap" v-show="isShow">
        <ul class="options">
          <li class="optionItem" :class="{selected:item.code === selected.code}" v-for="(item,index) in filterOptionData" :key='index' @click="select(item)">
            <img :src="item.image" />
            <span>{{item.en}} (+{{item.code}})</span>
          </li>
        </ul>
      </div>
    </div>
    <span class="errorText" v-if="isError">Required</span>
  </div>
</template>
<script>
export default {
  props: {
    value:String,
    optionData: Array,
    currentItem: Object,
    placeholder: String
  },
  data() {
    return {
      isShow:false,
      selected:{},
      isError:false,
      filter:null,
      filterOptionData:[]
    };
  },
  computed: {
    inputData: {
      get() {
        const data = this.value.split('/')[1]
        return data;
      },
      set(val) {
        const data = '+' + this.selected.code + '/' + val
        this.$emit("input", data);
      }
    },
  },
  methods:{
    select(item){
      this.selected = item
      this.isShow = false
    },
    check(){
      const data = this.value.split('/')[1]
      if(!data){
        this.isError = true
      }else{
        this.isError = false
      }
    }
  },
  watch:{
    currentItem(){
      this.selected = this.currentItem
    },
    selected(){
      //监听前缀变化
      this.inputData = this.inputData ? this.inputData : ''
    },
    isShow(){
      if(this.isShow === true){
        //搜索列表展示全部数据
        this.filter = '' 
        //搜索框自动获取焦点
        setTimeout(() => {
          this.$refs.search.focus()
        }, 100);
      }
    },
    filter(){
      let arr = []
      let val = new RegExp(this.filter,'gi')
      this.optionData.forEach( item => {
        if(item.en.match(val)){
          arr.push(item)
        }
      })
      this.filterOptionData = arr
    }
  }
};
</script>
<style lang="less" scoped>
#selectAndInput {
  position: relative;
  .placeholder{
    width: 100%;
    height: 50px;
  }
  .selectAndInputInner{
    position: absolute;
    top: 0;
    left: 0;
    z-index: 3;
    width: 100%;
    .inputWrap {
      height: 50px;
      .selectPrefix {
        position: absolute;
        left: 0;
        top: 0;
        width: 90px;
        height: 50px;
        background: #f2f2f2;
        border: 1px solid #828282;
        box-sizing: border-box;
        border-radius: 4px 0px 0px 0px;
        display: flex;
        align-items: center;
        justify-content: center;
        img {
          width: 22px;
          height: 14px;
        }
        span {
          font-size: 16px;
          color: #464646;
          margin-left: 10px;
        }
      }
      .prefixInput {
        width: 100%;
        height: 100%;
        outline: 0;
        padding-left: 100px;
        font-size: 16px;
        border: 1px solid #bdbdbd;
        box-sizing: border-box;
        border-radius: 4px;
        &::-webkit-input-placeholder {
          color: #959595;
        }
        &::-moz-input-placeholder {
          color: #959595;
        }
        &::-ms-input-placeholder {
          color: #959595;
        }
        &:focus{
          border:1px #7D45AB solid;
        }
        &.error{
          border: 1px #E11F2E solid;
          border-radius: 4px;
        }
      }
    }
    .search{
      position: absolute;
      width: 100%;
      height: 40px;
      font-size: 16px;
      box-sizing: border-box;
      padding-left: 10px;
      border:1px #7D45AB solid;
    }
    .optionsWrap {
      max-height: 240px;
      overflow-y: scroll;
      border-bottom:1px #7D45AB solid;
      border-radius: 0px 0px 4px 4px;
      margin-top: 40px;
      /* &::-webkit-scrollbar {
        width: 8px;
        height: 60px;
        border-right: 1px #7d45ab solid;
        background-color: #fff;
      }
      &::-webkit-scrollbar-thumb {
        border-radius: 3px;
        background-color: #E0E0E0;
      }
      &::-webkit-scrollbar-track{
        padding-right:4px;
      } */
      .options {
        width: 100%;
        border-radius: 0px 0px 4px 4px;
        border-left: 1px #7d45ab solid;
        border-right: 1px #7d45ab solid;
        .optionItem {
          height: 40px;
          background-color: #fff;
          display: flex;
          align-items: center;
          white-space: nowrap;
          text-overflow: ellipsis;
          overflow: hidden;
          img {
            width: 22px;
            height: 14px;
            margin: 0 10px;
          }
          span {
            font-size: 16px;
            color: #464646;
          }
          &:hover{
            background-color: #7D45AB;
            span{
              color: #fff;
            }
          }
          &.selected{
            background-color: #EEE7F4;
            span{
              color: #464646;
            }
          }
        }
      }
    }
  }
  .errorText{
    position: absolute;
    padding-left: 20px;
    padding-top: 8px;
    color: #E11F2E;
    font-size: 13px;
  }

}
</style>
