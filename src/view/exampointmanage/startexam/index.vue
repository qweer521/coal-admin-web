<template>
    <div class='startexam'>
        <Card>
            <p slot="title" class="card-title">
                <Icon type="home"></Icon>
                <span>考试申报</span>
            </p>
            <div>
                <template>
                   <div class='content-startexam'>
                       <p slot="header" style="text-align:center" class='header'>
                            <span>输入管理员验证码后方可开始考试</span>
                        </p>
                        <div style="text-align:center" class='verificationCode'>
                            <span>请输入验证码：</span>
                            <input :disabled="isValidate" v-model="verificationCode" type="text"/>
                        </div>
                        <div slot="footer" style="text-align:center" class='applicationExam'>
                            <Button :disabled="isValidate"  type="primary" style='margin-top:30px;' @click="applicationExam"> {{isValidate ? '申报考试验证中':'申报考试'}}</Button>
                        </div>
                   </div>
                </template>
            </div>
        </Card>
    </div>
</template>
<script>
    import dayjs from 'dayjs'
    import { post } from '@/libs/axios-cfg'
    export default {
        data () {
            return {
                verificationCode:"",
                isValidate:false,
            }
        },
        components:{

        },
        created(){
           
        },
        methods:{
            applicationExam () {
                if(this.isValidate){
                    return false;
                }
                if(this.verificationCode){
                    this.getData()
                }else{
                    this.$Message.warning("请输入验证码");
                }
                
            },
            /**
             * @description 获取考试申报管理列表
             */
            async getData(){
                //验证码
                this.isValidate = true;
                const loadings = this.$Message.loading({
                    content: '用户信息验证中...',
                    duration: 0
                });
                try {
                    let res = await post('/exam/site/apply/start',{
                         msgcode : this.verificationCode
                    })
                    this.$Message.success("申报考试成功");
                } catch (error) {
                    this.$throw(error)
                }
                setTimeout(loadings, 0);
                this.isValidate = false;
            },
        }
    }
</script>
<style>
    .startexam{
        height:100%; 
    }
    .startexam .ivu-card{
        height:100%;
    }
    .startexam .ivu-card-body{
        height:calc(100% - 51px);
        background-image: url("../../../assets/images/login_bg.png");
        background-size: 100% 100%;
    }
    .header{
        font-size: 22px;
        color:#fff;
        margin-top:30px;
        margin-bottom:30px
    }
    .verificationCode{
        font-size: 18px;
        color:#fff;
    }
    .content-startexam{
        width: 600px;
        height:220px;
        margin: 0 auto;
        position:absolute;
        top:50%;
        left:50%;
        transform: translate(-50%, -50%);
        background-image:url(../../../assets/images/login_boxbg.png);
        background-size: 100% 100%;
        text-align: center;
    }
</style>