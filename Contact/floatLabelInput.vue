<template>
  <div>
    <div id="floatContainer1" class="float-container" :class="{error:isShowError}">
      <label for="floatField1">{{label}}</label>
      <input type="text" id="floatField1" v-model="currentVal" data-placeholder
        :maxlength="maxLength"
        @focus="addPrefix"
        @blur="check(currentVal)"/>
    </div>
    <span class="errorText" v-if="isShowError">{{formatInfo.errorText}}</span>
  </div>
</template>

<script>
import { FloatLabel } from "./floatLabel";

export default {
  props: {
    value: {
      type: [String, Number, Object, Array],
    },
    label: {
      type:String
    },
    formatInfo:{
      type:Object
    },
    prefix:{
      type:String
    },
    maxLength:{
      type:Number
    },
  },
  data() {
    return {
      isError:false
    };
  },
  computed: {
    currentVal: {
      get() {
        return this.value;
      },
      set(val) {
        this.$emit("input", val);
      }
    },
    isShowError(){
      return this.formatInfo && this.isError
    }
  },
  methods:{
    check(val){
      if(!this.formatInfo)return
      this.isError = this.formatInfo.pattern && !this.formatInfo.pattern.test(val)
    },
    addPrefix(){
      if(!this.prefix)return
      this.currentVal = this.currentVal ? this.currentVal : this.prefix
    }
  },
  mounted(){
    FloatLabel.init()
  }
};
</script>

<style lang="less" scoped>
/** float container */
.float-container {
  position: relative;
  font-family: Roboto;
  font-size: 16px;
  line-height: 16px;
  box-sizing: border-box;
  height: 50px;
  border: 1px solid #bdbdbd;
  border-radius: 4px;
  input {
    border: none;
    padding-left: 20px;
    font-size: 16px;
    outline: 0;
    width: 100%;
    height: 100%;
    padding-top: 8px;
    opacity: .1;
  }
  label {
    font-size: 16px;
    position: absolute;
    transform-origin: top left;
    transform: translate(0, 16px) scale(1);
    transition: all 0.1s ease-in-out; //speed
    padding-left: 20px;
    color: #959595;
    margin-bottom: 0;
    z-index: 0;
  }
  /** active label */
  &.active {
    border: 1px solid #4d008c;
    border-left: 6px solid #4d008c;
    input{
      border-radius: 4px;
      opacity: 1;
    }
    label {
      top: 8px;
      left: 20px;
      font-size: 10px;
      line-height: 10px;
      padding-left: 0;
      color: #4d008c;
      //move the x coordinate and reduce size
      transform: translate(0, 4px) scale(0.75);
      z-index: 0;
    }
  }
  /** error */
  &.error {
    border: 1px solid #E11F2E;
    border-left: 6px solid #E11F2E;
    label{
      top: 8px;
      left: 20px;
      font-size: 10px;
      line-height: 10px;
      padding-left: 0;
      color: #E11F2E;
      //move the x coordinate and reduce size
      transform: translate(0, 4px) scale(0.75);
    }
    input{
      color: #E11F2E;
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
</style>
