<template>
  <div id="selectWrap">
    <div class="select" :class="{active:isFocus}" @click="handleSelectClick">
      <label for="floatField">{{selectTitle}}</label>
      <input type="text" id="floatField" :value="inputData" disabled="disabled">
      <div class="angle">
        <div class="angleItem angleTop"></div>
        <div class="angleItem angleBottom"></div>
      </div>
    </div>
    <div class="optionsWrap" v-if="isShow">
      <ul class="options">
        <li class="option" :class="{selected:option === currentOption}" v-for="(option,index) in selectOptions" :key='index' @click="handleOptionClick(option)">{{option}}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import { FloatLabel } from "./floatLabel";

export default {
  props: {
    selectTitle:String,
    selectOptions:Array,
    currentOption:String
  },
  data() {
    return {
      isFocus:false,
      isShow:false
    };
  },
  computed:{
    inputData:{
      get(){
        return this.isFocus?this.currentOption:''
      },
      set(val){
        this.$emit("input", val);
      }
    }
  },
  methods:{
    handleSelectClick(){
      this.isFocus = true
      this.isShow = !this.isShow
    },
    handleOptionClick(option){
      this.inputData = option
      this.isShow = !this.isShow
    }
  }
};
</script>

<style lang="less" scoped>
#selectWrap{
  position: relative;
  .select{
    box-sizing: border-box;
    border: 1px solid #BDBDBD;
    width: 100%;
    height: 50px;
    border-radius: 4px;
    input {
      width: 100%;
      height: 100%;
      outline: 0;
      border: none;
      border-radius: 4px;
    }
    label {
      position: absolute;
      top: -3px;
      left: 20px;
      font-size: 16px;
      color: #959595;
      transform-origin: top left;
      transform: translate(0, 16px) scale(1);
      transition: all 0.1s ease-in-out; //speed
    }
    .angle{
      position: absolute;
      right: 8px;
      top: 0;
      height: 100%;
      width: 10px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      .angleTop{
        width:0;
        height:0;
        border-left:4px solid transparent;
        border-right:4px solid transparent;
        border-bottom:8px solid #BDBDBD;
      }
      .angleBottom{
        margin-top: 1px;
        width:0;
        height:0;
        border-left:4px solid transparent;
        border-right:4px solid transparent;
        border-top:8px solid #BDBDBD;
      }
    }
    /** active label */
    &.active {
      border: #4d008c 1px solid;
      input{
        font-family: Roboto;
        font-size: 16px;
        line-height: 16px;        
        color: #4F4F4F;
        padding-left: 20px;
        padding-top: 6px;
      }
      label {
        top: 8px;
        left: 18px;
        font-size: 10px;
        line-height: 10px;
        padding-left: 0;
        color: #4d008c;
        //move the x coordinate and reduce size
        transform: translate(0, 4px) scale(0.75);
        z-index: 0;
      }
    }
  }
  .optionsWrap{
    position: absolute;
    top: 50px;
    left: 0;
    width: 100%;
    max-height: 240px;
    overflow: auto;
    background-color: #fff;
    border: 1px solid #4D008C;
    border-top: none; 
    border-radius: 0 0 4px 4px;
    box-sizing: border-box;
    z-index: 5;
    .options{
      .option{
        box-sizing: border-box;
        padding-left: 20px;
        width: 100%;
        height: 40px;
        line-height: 40px;
        font-size: 14px;
        color: #4F4F4F;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
        &:hover{
          background-color: #7D45AB;
          color: #fff;
        }
        &.selected{
          background-color: #EEE7F4;
          color: #4F4F4F;
        }
      }
    }
  }
}
</style>
