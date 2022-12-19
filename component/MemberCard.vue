<template>
    <div style="width:190px;height:130px;margin-left:10px;margin:5px;margin-bottom:0px;float:left;border-radius:8px;padding:5px" :class="(pps_MemberInfo.Member_GroupValue=='1')?'MemberCard_GroupBorder1':(pps_MemberInfo.Member_GroupValue=='2')?'MemberCard_GroupBorder2':'MemberCard_GroupBorder3'">
        <div style="width:80%">
            <div style="float:left;width:100%;text-align: left;margin:3px;overflow:hidden;white-space: nowrap">姓名：{{pps_MemberInfo.Member_Name}}</div>
            <div style="float:left;width:100%;text-align: left;margin:3px">生日：{{pps_MemberInfo.Member_Birthday}}</div>
            <div style="float:left;width:100%;text-align: left;margin:3px">組別：{{pps_MemberInfo.Member_Group}}</div>
            <div style="float:left;width:100%;text-align: left;margin:3px;word-break:break-all">技能：{{pps_MemberInfo.Member_Skill}}</div>
        </div>
        <div style="text-align:right">
            <button @click="cmdDelete()">X</button>
        </div>
    </div>
</template>
  
<script>
    export default {
        data: function () {
            return {
                myMemberInfo:[]
            }
        },
        created: function () {
            
        },
        mounted: function() {
            this.Get_Props("myMemberInfo",this.pps_MemberInfo);
        },
        methods:{
            Get_Props: function(Data_Key,pps){
                if(pps != undefined){
                    this[Data_Key] = pps;
                }
            },
            cmdDelete: function(){
                let myReturn = this.pps_MemberInfo.Member_ID;
                this.$emit('cmdDelete_MembarCard', myReturn);
            }
        },
        props: {
            pps_MemberInfo:[]
        }
    }
</script>

<style>
    .MemberCard_GroupBorder1{
        border:1px solid white;
    }

    .MemberCard_GroupBorder2{
        border:1px solid blue;
    }

    .MemberCard_GroupBorder3{
        border:1px solid Green;
    }
</style>