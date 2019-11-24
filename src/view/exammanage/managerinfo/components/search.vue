<template>
    <div style='margin-bottom:10px;'>
        <Row>
            <Col span="24">
                <Button type="primary" style='margin-bottom:10px;' @click="exportData"><Icon type="ios-download-outline"></Icon>&nbsp;成绩导出</Button>
            </Col>
        </Row>
        <div class='searchContainer'>
            <span>考点信息：</span>
            <Input v-model.trim="param.applysite" type="text"></Input>
        </div>
        <div class='searchContainer starttime'>
            <span>考试开始时间：</span>
            <Date-picker  placeholder="选择日期"  type="datetime" v-model="param.starttime"  :key="param.starttime"  format="yyyy-MM-dd"  @on-change="param.starttime=$event" >
            </Date-picker> 
        </div>
        <div class='searchContainer endtime'>
            <span>考试结束时间：</span>
            <Date-picker  placeholder="选择日期"  type="datetime" v-model="param.endtime"  :key="param.endtime"  format="yyyy-MM-dd"  @on-change="param.endtime=$event" >
            </Date-picker>  
        </div>
        <div class='searchContainer'>
            <span>考生姓名：</span>
            <Input v-model.trim="param.username" type="text"></Input>
        </div>
        <div class='searchBtn'>
            <Button type="primary" :loading="loading" @click="search">搜索</Button>
        </div>
        
    </div>
</template>
<script>
import { post } from '@/libs/axios-cfg'
export default {
    data(){
        return {
            loading:false,
        }
    },
    props:{
        param: {
            type: Object,
            default: {
                applysite:"",
                starttime:"",
                endtime:"",
                username:""
            }
        }
    },
    methods:{
        /**
         * @description 搜索
         * @param 搜索
         */
        search() {
            this.$emit("search", this.param);
        },
         /**
         * @description 题库模板下载
         */
        exportData(){
            window.open('http://221.11.24.244:18082/exam/manager/result/export', '_self');
        }  
    }
}
</script>
<style>
    .searchContainer{
        display: inline-block;
        width:22%;
    }
    .searchBtn{
        float: right;
    }
    .searchContainer{
        margin-bottom:10px;
    }
    .searchContainer>span{
        display: inline-block;
        color:#495060;
    }
    .starttime,.endtime{
        margin-right:20px;
    }
    .searchContainer .ivu-input-wrapper,.searchContainer .ivu-date-picker{
        width:calc(100% - 100px);
    }
    .searchContainer .ivu-date-picker-rel .ivu-input-wrapper{
        width:100%;
    }
</style>
