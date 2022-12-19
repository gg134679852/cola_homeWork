<template>
    <div>
        <div style="width:1050px;margin:0 auto;">
          <div style="text-align: right">
          
          </div>

          <div style="background-color:deepskyblue;width:100%;height:50px;position:relative">
            <div style="width:60%;height:100%;padding-left:10px">
              <div style="width:50%;padding-top:20px;float:left">
                資料顯示：
                <button style="width:50px;height:25px" @click="Get_ViewData('all')">全部</button>
                <button style="width:50px;height:25px" @click="Get_ViewData('group')">組別</button>
                <button style="width:50px;height:25px" @click="Get_ViewData('skill')">技能</button>
              </div>
              <div style="width:50%;padding-top:20px;float:left;text-align:right">
                排序：
                <button style="width:70px;height:25px" @click="Sort_ViewData('name')">姓名排序</button>
                <button style="width:70px;height:25px" @click="Sort_ViewData('birthday')">生日排序</button>
                <button style="width:70px;height:25px" @click="Sort_ViewData('group')">組別排序</button>
              </div>
            </div>
            <div style="position:absolute;bottom:0;right:0;width:100px;text-align:right;">{{myToday}}</div>
          </div>

          <div style="width:100%">
            <div style="float:left;background-color:coral;width:65%;height:500px;overflow:auto" v-show="myViewType=='all'">
              <MemberCard v-for="wkInfo in myCompanyInfo" :pps_MemberInfo="wkInfo" @cmdDelete_MembarCard="cmdDelete"></MemberCard>
            </div>

            <div style="float:left;background-color:coral;width:65%;height:500px;overflow:auto" v-show="myViewType=='group'||myViewType=='skill'">
              <div v-for="wkData in myViewData" style="margin-left:15px">
                <div>{{wkData.View_Title + "："}}</div>
                <ul style="list-style:none;display:flex;margin-left:0px;padding-left:0px">
                  <li>
                    <div v-for="wkMember in wkData.View_Members" style="width:190px;margin-left:10px;margin:5px;margin-bottom:0px;float:left;border:1px solid black;border-radius:8px;padding:5px">
                      <div style="float:left;width:100%;text-align: left;margin:3px">姓名：{{wkMember.Member_Name}}</div>
                      <div style="float:left;width:100%;text-align: left;margin:3px">生日：{{wkMember.Member_Birthday}}</div>
                      <div style="float:left;width:100%;text-align: left;margin:3px">組別：{{wkMember.Member_Group}}</div>
                      <div style="float:left;width:100%;text-align: left;margin:3px">技能：{{wkMember.Member_Skill}}</div>
                    </div>
                  </li>
                </ul>
              </div>
            </div>
            
            <div style="float:left;background-color:seagreen;width:35%;height:500px;position:relative">
              <div style="margin:10px;margin-top:30px">
                <div style="float:left;width:70px;text-align: right;">姓名：</div>
                <div><input type="text" style="width:80px" v-model="myName" /></div>
                <br/>
                <div style="float:left;width:70px;text-align: right;">生日：</div>
                <div><input type="text" style="width:80px" v-model="myBirthday" /></div>
                <br/>
                <div style="float:left;width:70px;text-align: right;">組別：</div>
                <div v-for="wkGroup in myGroupList" style="float:left"><input type="radio" name="rdoGroup" v-model="myGroup" :value="wkGroup">{{wkGroup.Option_Text}}</div>
                <br/><br/>
                <div style="float:left;width:70px;text-align: right;">技能：</div>
                <div style="float:left">
                  <div style="cursor:default" @click="Set_SkillAllMark()"><input type="checkbox" name="chkSkill" :checked="mySkill_AllMark">全部</div>
                  <div v-for="wkSkill in mySkillList" style="cursor:default" @click="Set_Skill()"><input type="checkbox" name="chkSkill" v-model="mySkill" :value="wkSkill.Option_Value">{{wkSkill.Option_Text}}</div>
                </div>

                <div style="position:absolute;bottom:0;right:0;width:100px;text-align:right;margin:10px">
                  <button style="width:70px;height:30px;font-size:16px" @click="cmdInsert()">新增</button>
                </div>
              </div>
            </div>
          </div>

        </div>
    </div>
  </template>
  
<script>
  import axios from "axios";
  import * as https from "https";
  import MemberCard from "/components/MemberCard.vue";
  import niceSelect from "/components/niceSelect.vue";
  import {M10CTour_GetGroupList,M10CTour_GetSkillList} from "/assets/Instance.js";

  const httpsAgent = new https.Agent({
      rejectUnauthorized: false,
  });
  const ignoreSSL = axios.create({
          httpsAgent: new https.Agent({
          rejectUnauthorized: false,
      }),
  });
  const agent = new https.Agent({
      rejectUnauthorized: false,
  }); 

  export default {
    head () {
      return {
        script: [
          {
              src: '/COLA_AppFiles/A000_WebControls/js/axios_test.js'
          }
        ]
      }
    },
    data(){
      return {
        name: 'IndexPage',
        myToday:"",
        myName:"",
        myBirthday:"",
        myGroup:"",
        mySkill_AllMark:false,
        mySkill:[],
        myMemberID:1,
        myGroupList:[],
        mySkillList:[
            {
              Option_Text:"Vue",
              Option_Value:"Vue",
            },
            {
              Option_Text:"React",
              Option_Value:"React",
            },
            {
              Option_Text:"Angular",
              Option_Value:"Angular",
            },
            {
              Option_Text:"Javascript",
              Option_Value:"Javascript",
            },
            {
              Option_Text:"CSS",
              Option_Value:"CSS",
            },
            {
              Option_Text:"JQuery",
              Option_Value:"JQuery",
            }
          ],
        myCompanyInfo:[],
        myViewType:"all",
        myViewData:[],
        mySortType:"name"
      }
    },
    methods:{
      cmdTest: function(returnValue){
        console.log(returnValue);
      },
      cmdInsert: function(){
        if(this.myName == ""){
          alert("請輸入姓名");
          return;
        }

        let r = /^[a-zA-Z\u4E00-\u9FA5]+$/
        if(!r.test(this.myName)){
          alert("姓名格式錯誤");
          return;
        }

        let myDate = new Date(this.myBirthday);

        if(isNaN(myDate)){
          alert("生日格式輸入錯誤");
          return;
        }

        let myBirthdayFormat = myDate.getFullYear() + "/" + ((myDate.getMonth() + 1) < 10 ? ("0" + (myDate.getMonth() + 1)) : (myDate.getMonth() + 1)) + "/" + (myDate.getDate() < 10 ? ("0" + myDate.getDate()) : myDate.getDate());

        let mySkillString = "";
        if(this.mySkill_AllMark){
          for(let i=0;i<this.mySkillList.length;i++){
            mySkillString += this.mySkillList[i].Option_Value + ",";
          }
        }
        else{
          for(let i=0;i<this.mySkill.length;i++){
            mySkillString += this.mySkill[i] + ",";
          }
        }

        if(mySkillString != ""){
          mySkillString = mySkillString.substring(0,mySkillString.length-1)
        }
        
        let wkInfo = {
          Member_ID:this.myMemberID,
          Member_Name:this.myName,
          Member_Birthday:myBirthdayFormat,
          Member_Group:(this.myGroup.length==0)?"":this.myGroup.Option_Text,
          Member_GroupValue:(this.myGroup.length==0)?"":this.myGroup.Option_Value,
          Member_Skill:mySkillString
        };
        
        this.myCompanyInfo.push(wkInfo);
        this.myMemberID++;
      },
      cmdDelete: function(value){
        for(let i=0;i<this.myCompanyInfo.length;i++){
          if(this.myCompanyInfo[i].Member_ID == value){
            this.myCompanyInfo.splice(i,1);
            break;
          }
        }
      },
      Set_SkillAllMark: function(){
        this.mySkill_AllMark = !this.mySkill_AllMark;

        if(this.mySkill_AllMark){
          this.mySkill = [];
        }
      },
      Set_Skill: function(){
        this.mySkill_AllMark = false;
      },
      Get_ViewData: function(Type){
        let myViewList = [];
        let myKeyWord = "";
        let wkObject;
        
        this.myViewType = Type;

        switch(Type){
          case "group":
            myViewList = this.myGroupList;
            myKeyWord = "Member_GroupValue";
            break;
          case "skill":
            myViewList = this.mySkillList;
            myKeyWord = "Member_Skill";
            break;
        }

        this.myViewData = [];
        for(let i=0;i<myViewList.length;i++){
          wkObject = {
            View_Title:myViewList[i].Option_Text,
            View_Members:[]
          };

          for(let j=0;j<this.myCompanyInfo.length;j++){  
            if(this.myCompanyInfo[j][myKeyWord].indexOf(myViewList[i].Option_Value) != -1){
              wkObject.View_Members.push(this.myCompanyInfo[j])
            }
          }

          this.myViewData.push(wkObject);
          wkObject = null;
        }

        console.log(this.myViewData);
      },
      Sort_ViewData: function(Type){
        let myFunction;

        switch(Type){
          case "name":
            myFunction = function Sort(a, b) {
                return (a.Member_Name < b.Member_Name) ? 1 : ((a.Member_Name > b.Member_Name) ? -1 : 0);
            };
            break;

          case "birthday":
            myFunction = function Sort(a, b) {
                return (new Date(a.Member_Birthday) < new Date(b.Member_Birthday)) ? 1 : ((new Date(a.Member_Birthday) > new Date(b.Member_Birthday)) ? -1 : 0);
            };
            break;

          case "group":
            myFunction = function Sort(a, b) {
                return (a.Member_GroupValue < b.Member_GroupValue) ? 1 : ((a.Member_GroupValue > b.Member_GroupValue) ? -1 : 0);
            };
            break;
        }

        switch(this.myViewType){
          case "all":
            this.myCompanyInfo.sort(myFunction);
            break;
          case "group":
          case "skill":
            for(let i=0;i<this.myViewData.length;i++){
              this.myViewData[i].View_Members.sort(myFunction);
            }
            break;
        }
      }
    },
    created: function(){
      
    },
    mounted: function(){
      let myDate = new Date();
      this.myToday = myDate.getFullYear() + "/" + ((myDate.getMonth() + 1) < 10 ? ("0" + (myDate.getMonth() + 1)) : (myDate.getMonth() + 1)) + "/" + (myDate.getDate() < 10 ? ("0" + myDate.getDate()) : myDate.getDate())

      let T00S = this;
      let wkGroup = {
        Group_Count:2
      }
      M10CTour_GetGroupList({params:wkGroup, httpsAgent: agent})
      .then(function (response){
        T00S.myGroupList = response.data;
      })
      
      M10CTour_GetSkillList({params:{Develop_Type:"back",Today:this.myToday}, httpsAgent: agent})
      .then(function (response){
        T00S.mySkillList = response.data;
      })
    },
    component:{
      MemberCard,
      niceSelect,
    },
    async asyncData() {
        
    }, 
  }
</script>
  
<style>
</style>