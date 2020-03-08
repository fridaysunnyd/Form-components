<template>
  <div id="contact" class="contact">
    <Modal
      v-model="robotModalflag"
      title="Verify"
      :mask-closable="false"
      :scrollable="true"
    >
      <div id="robotLogin"></div>
      <p slot="footer"></p>
    </Modal>
    <div class="hero">
      <h1 class="title">CONTACT US</h1>
    </div>
    <div class="form-content">
      <form autocomplete="off">
        <div class="over-hidden">
          <ul>
            <li class="f-left w380">
              <FloatLabelInput v-model="formVal.firName" :label="'First Name'" v-if="isHackReset"
              :formatInfo='{errorText:"Required",pattern:/\S/}'/>
              <p class="p_surnames"></p>
            </li>
            <li class="f-right w380 m-t-sm-40">
              <FloatLabelInput v-model="formVal.lastName" :label="'Last Name'" v-if="isHackReset"
              :formatInfo='{errorText:"Required",pattern:/\S/}'/>
              <p class="p_surnames"></p>
            </li>
          </ul>
        </div>
        <div class="m-t-40 m-t-sm-40 w-full">
          <FloatLabelInput v-model="formVal.Email" :label="'Email'" v-if="isHackReset"
          :formatInfo='{errorText:"Invalid email address format",pattern:/\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*/}'/>
          <p class="p_email"></p>
        </div>
        <div class="m-t-40 m-t-sm-40 w-full">
          <!-- <FloatLabelInput v-model="formVal.phone" :label="'Phone Number'" v-if="isHackReset"
          :formatInfo='{errorText:"Required",pattern:/\S/}'/>
          <p class="p_tel"></p> -->
          <SelectAndInput :placeholder="'Phone Number'" :optionData='cityDate' :currentItem='cityDate[0]' v-model='formVal.phone'/>
        </div>
        <div class="m-t-40 m-t-sm-40">
          <ul class="selectRow">
            <li class="w380">
              <FloatLabelInput v-model="formVal.company" :label="'Company'" v-if="isHackReset"
              :maxLength="50"
              :formatInfo='{errorText:"2 character minimum, 50 character maximum",pattern:/^.{2,50}$/}'/>
              <p class="p_num"></p>
            </li>
            <li class="m-t-sm-40 w380">
              <Select :selectTitle="positionSelectTitle" :selectOptions="positionSelectOptions" :currentOption="position" v-model='position' v-if="isHackReset"/>
              <p class="p_name"></p>
            </li>
          </ul>
        </div>
        <div class="m-t-40 w-full m-t-sm-40">
          <FloatLabelInput v-model="formVal.Website" :label="'Company Website'" v-if="isHackReset"
            :maxLength="200"
            :prefix="'https://'"
            :formatInfo='{errorText:"Invalid company website format",pattern:/^((https?|ftp|file):\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/}'/>
          <p class="p_num"></p>
        </div>
        <div class="m-t-40 m-t-sm-40">
          <Select :selectTitle="whySelectTitle" :selectOptions="whySelectOptions" :currentOption="why" v-model='why' v-if="isHackReset"/>
          <p class="p_checkbox1"></p>
        </div>
        <div class="m-t-40 m-t-sm-40">
          <textarea
            style="resize:none;font-size:16px;color:#000"
            class="api-input w-full texts"
            id="exampleFormControlTextarea1"
            v-model="formVal.Comments"
            placeholder="Comments"
            maxlength="10000"
          ></textarea>
        </div>
        <div class="m-t-40 m-t-sm-40 myBtn">
          <div id="submitbtn" class="btn_middle Pill_Button" :class="{Pill_Button_Disabled:isLock}" href="#" @click="submit" value="Submit">Submit</div>
        </div>
      </form>
    </div>
    <!-- 成功提交对话框 -->
    <Modal
      v-model="submitSucssesModal"
      :mask-closable="false"
      @on-ok="()=>this.submitSucssesModal = false"
      @on-cancel="()=>this.submitSucssesModal = false">
        <p style="text-align: center;font-size: 16px;color: #000;">Thank you for submitting your information. </p>
        <p style="text-align: center;font-size: 16px;color: #000;">We will contact you after reviewing your application internally.</p>
    </Modal>
  </div>
</template>
<script>
import { getApi } from '../../../api/axios.js';
import { getSubmit } from "@/api/contact";
import { onloadCallback } from "../../api/usersystem.js";
import FloatLabelInput from './floatLabelInput'
import SelectAndInput from './selectAndInput'
import Select from './select'
import { components } from "@/components/css/style.css";

export default {
  data() {
    return {
      position: "CEO",
      why: "Connecting an exchange",
      isSuccess: false,
      isError: false,
      timer: null,
      errVal: "Error",
      formVal: {
        firName: "",
        lastName: "",
        Email: "",
        phone: "",
        company: "",
        Website: "",
        Comments: ""
      },
      robotModalflag: false,
      single: null,
      isHackReset: true,
      cityDate:[],
      whySelectTitle:'What services are you interested in?',
      whySelectOptions:[
        'Connecting an exchange',
        'Issuing a project token',
        'Trading on an Apifiny platform',
        'Connecting trading services',
        'Stablecoin listing',
        'Other'
      ],
      positionSelectTitle:'Title',
      positionSelectOptions:[
        'CEO',
        'CXO',
        'VP',
        'BD Manager',
        'Other',
      ],
      submitSucssesModal: false
    };
  },
  computed:{
    isLock(){
      //与输入框组件校验统一，有标红按钮锁住
      let required = true
      let correct = true
      /* for (const key in this.formVal) {
        if (!this.formVal[key]) {
          required = false
        }
      } */
      if(!this.formVal.firName || !this.formVal.lastName || !this.formVal.Email || !this.formVal.phone.split('/')[1]){
        required = false
      }
      correct = this.formVal.Email.match(/\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*/)
      && this.formVal.company.match(/^.{2,50}$/)
      && (!this.formVal.Website || this.formVal.Website.match(/^((https?|ftp|file):\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/))//没有网址或网址符合规范为true
      return !required || !correct
    }
  },
  methods: {
    submit() {
      //与按钮状态统一，置灰不能提交
      if(this.isLock){
        return
      }
      //下拉框必选
      if(this.position === 'Title'){
        this.$Message.error('Title is required')
        return
      }
      if(this.why === 'What services are you interested in?'){
        this.$Message.error('Services is required')
        return
      }
      let params = {
        firstName: this.formVal.firName,
        lastName: this.formVal.lastName,
        email: this.formVal.Email,
        phone: this.formVal.phone,
        JobTitle: this.position,
        CompanyName: this.formVal.company,
        CompanyWebsite: this.formVal.Website,
        BuinessType: this.why,
        ApifinyMes: this.formVal.Comments
      };
      let _that = this;
      try {
        /* if (!this.formVal.firName) {
          throw "First Name is required";
        }
        if (!this.formVal.lastName) {
          throw "Last Name is required";
        }
        if (!this.formVal.Email) {
          throw "Email is required";
        }
        if (!this.formVal.phone) {
          throw "Phone Number is required";
        }
        if (!this.formVal.company) {
          throw "Company is required";
        }
        if (this.position === "Title") {
          throw "Title is required";
        }
        if (!this.formVal.Website) {
          throw "Company Website is required";
        }
        if (this.why === "What services are you interested in?") {
          throw "Services is required";
        } */
        this.robotModalflag = true;
        if (_that.single || _that.single == 0) {
          return false;
        }
        this.single = onloadCallback(
          "robotLogin",
          function(res) {
            if (res) {
              getSubmit({
                personType: "GOOGLE",
                personCode:res,
                form: params,
                businessType:'contact'
              }).then(res => {
                if (res.result) {
                 _that.robotModalflag = false;
                 _that.$Message.success('Success')
                 _that.submitSucssesModal = true
                  /* _that.isSuccess = true;
                  setTimeout(() => {
                    _that.isSuccess = false;
                  }, 1500); */
                  _that.empty();
                  _that.isHackReset = false
                  _that.$nextTick(() => {
                    _that.isHackReset = true
                  })
                } else {
                 _that.robotModalflag = false;
                  _that.errVal = "Error";
                  _that.$Message.success('Fail')
                  /* _that.isError = true;
                  setTimeout(() => {
                    _that.isError = false;
                  }, 1500); */
                }
              })
            }
          },
          function(err) {
            _that.robotModalflag = false;
            _that.loaded = true;
          },
          function(netError) {
            _that.robotModalflag = false;
            _that.loaded = true;
          }
        );
      } catch (e) {
        _that.errVal = e;
        _that.isError = true;
        setTimeout(() => {
          _that.isError = false;
        }, 1500);
      }
    },
    // Sense.judge(
    //     {
    //       id: "168014ae2e25f7d2c9cebe9527975d56",
    //       interactive: 2, //场景1:注册;2:登录;
    //       area: "#area", // 如果有验证码，可以设置验证码的位置
    //       bg_color: "red", // 如果有验证码，可以设置验证码的背景颜色
    //       width: "310px" // 如果有验证码，可以设置验证码的宽度
    //     },function(data) {
    //       getSubmit({
    //         personType:'GEETEST',
    //         personCode:data.challenge,
    //         form: params
    //       }).then(res => {
    //         console.log(res);
    //         if (res.result) {
    //           that.isSuccess = true
    //           setTimeout(() => {
    //             that.isSuccess = false
    //            }, 3000);
    //            that.empty()
    //         }else{
    //           that.errVal = 'Error'
    //           that.isError = true
    //           setTimeout(() => {
    //             that.isError = false
    //            }, 3000);
    //         }
    //       })
    //     },function(err) {
    //       this.isError = true
    //       setTimeout(() => {
    //         this.isError = false
    //       }, 3000);
    //     }
    //   )
    empty() {
      Object.keys(this.formVal).map(v => {
        this.formVal[v] = "";
      });
    }
  },
  mounted() {
    //this.empty();
    getApi('https://oss.55com.io/content/country/55-country.json',{}).then((res)=>{
      const arr = res.slice(4)
      this.cityDate = arr
    })
  },
  components:{
    FloatLabelInput,
    SelectAndInput,
    Select
  }
};
</script>
<style lang="less" scoped>
@import url("../../components/css/style.css");
</style>
<style  lang="less">
#contact {
  .hero {
    width: 100%;
    height: 680px;
    background: url(images/Hero-Mask.png) no-repeat center center;
    background-size: 100% 100%;
    overflow: hidden;
    .title {
      margin-top: 350px;
      text-align: center;
      text-shadow: 0px 3px 10px #540091;
    }
  }
  .form-content {
    width: 800px;
    margin: 0 auto;
    padding: 120px 0px 200px;
    .api-input {
      box-shadow: none;
      line-height: 18px;
      padding: 10px 16px;
      border-radius: 5px;
      border: 1px solid #bdbdbd;
      color: #959595;
      box-sizing: border-box;
      &.err{
        border: 1px solid #E11F2E;
      }
    }
    .api-input:focus {
      box-shadow: white;
      outline: none;
      border: 1px solid #4d008c;
    }
    .w380 {
      width: 380px;
    }
    .w-full {
      width: 100%;
    }
    .m-t-40 {
      margin-top: 40px;
    }
    .over-hidden {
      overflow: hidden;
      .f-left {
        float: left;
      }
      .f-right {
        float: right;
      }
    }
    .submitbtn {
      width: 140px;
      height: 50px;
      background: #73eedc;
      border-radius: 35px;
      font-family: Rift;
      font-weight: 500;
      font-size: 20px;
      line-height: 50px;
      align-items: center;
      text-align: center;
      color: #2b0056;
      cursor: pointer;
      margin: 0 auto;
      &.lock{
        background: #E0E0E0;
        border-radius: 35px;
        cursor: not-allowed;
        color: #4F4F4F;
        letter-spacing: 1px;
      }
    }
    .myBtn{
      display: flex;
      justify-content: center;
    }
  }
  .selectRow{
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
  }
  @media (max-width: 998px) {
    .hero {
      height: 380px;
      .title {
        margin-top: 150px;
      }
    }
    .form-content {
      width: 100%;
      margin: 0 auto;
      padding: 60px 15px;
      .api-input {
        width: 100%;
      }
      .w380 {
        width: 100%;
      }
      .w-full {
        width: 100%;
      }
      .m-t-40 {
        margin-top: 20px;
      }
      .m-t-sm-40 {
        margin-top: 40px;
      }
      .lastname {
        margin-top: 20px;
      }
      #JobTitle {
        width: 100%;
      }
      .over-hidden {
        overflow: hidden;
        .f-left {
          float: none;
        }
        .f-right {
          float: none;
        }
      }
      .submitbtn {
        width: 140px;
      }
    }
  }
  .Success {
    color: green;
    border: solid 1px green;
  }
  .Error {
    color: red;
    border: solid 1px red;
  }
  .btn {
    width: 250px;
    height: 40px;
    border-radius: 4px;
    text-align: center;
    // line-height: 40px;
    position: fixed;
    // top:5% css改为局部了;
    left: 0;
    right: 0;
    margin: auto;
    background: #fff;
    opacity: 0.8;
  }
  @keyframes slideInUp {
    0% {
      top: 0%;
    }

    to {
      top: 10%;
    }
  }
  .slideIn {
    animation: slideInUp 1s ease-out;
  }
}

 @media (max-width: 577px){
    .hero {
      height: 300px !important;
      .title {
        margin-top: 150px;
      }
    }
 }
</style>




