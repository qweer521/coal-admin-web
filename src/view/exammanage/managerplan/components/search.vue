<template>
    <div style='margin-bottom:10px;'>
        <div class='searchContainer'>
            <span>考点信息：</span>
            <Input v-model.trim="param.applysite" type="text"></Input>
        </div>
        <div class='searchContainer'>
            <span>考试开始时间：</span>
            <Date-picker  placeholder="选择日期"  type="datetime" v-model="param.starttime"  :key="param.starttime"  format="yyyy-MM-dd"  @on-change="param.starttime=$event" >
            </Date-picker>  
        </div>
        <div class='searchContainer'>
            <span>考试结束时间：</span>
            <Date-picker  placeholder="选择日期"  type="datetime" v-model="param.endtime"  :key="param.endtime"  format="yyyy-MM-dd"  @on-change="param.endtime=$event" >
            </Date-picker>  
        </div>
        <Button class='searchBtn' type="primary" :loading="loading" @click="search">搜索</Button>
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
                endtime:""
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
            
    }
}
</script>
<style>
    .searchContainer{
        display: inline-block;
        width:30%;
        color:#495060;
    }
    .searchContainer .ivu-input-wrapper{
        width:calc(100% - 100px);
    }
    .searchBtn{
        float: right;
    }
    .searchContainer .ivu-date-picker-rel .ivu-input-wrapper{
        width:100%;
    }
</style>
