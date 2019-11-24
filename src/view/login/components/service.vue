<template>
  <div class="login">
      
    <Row  class="login-model">
        <Col span="24">
            <Card>
              <div style="text-align:center;padding:10px">
                <span class='loginTitle'>煤矿考试管理系统</span>
                <br>
                <Form @submit.native.prevent="handleSubmit" ref="formItem" :model="formItem" :rules="FromRule" >
                    <FormItem prop="account">
                      <label class='title'>用户名 ：</label>
                      <Input :disabled="isValidate" v-model.trim="formItem.account" type="text" placeholder="请输入用户名">
                      </Input>
                    </FormItem>
                    <FormItem prop="password">
                        <label class='title'>密码 ：</label>
                        <Input :disabled="isValidate" v-model.trim="formItem.password" type="password" placeholder="请输入密码">
                        </Input>
                    </FormItem>
                    <FormItem>
                        <Checkbox :disabled="isValidate" v-model="formItem.agree">下次登录记住我的身份</Checkbox><br>
                        <Button :loading="isSubmit"  :disabled="isValidate" html-type="submit" type="primary">
                            {{isValidate ? '登录验证中':'立即登录'}}
                        </Button>
                    </FormItem>
                </Form>
              </div>
          </Card>
        </Col>
    </Row>
  </div>
</template>
<script>
  import md5 from 'js-md5';
  import { post } from '@/libs/axios-cfg'
  export default {
        data () {
            return {
                isSubmit:false,
                formItem: {
                    account:'',
                    password:'',
                    validateCode:'',
                    agree:true
                },
                isValidate:false,
                FromRule:{
                    account:[
                        { required: true, message: '账号不能为空' }
                    ],
                    password:[
                        {required: true, message: '请填写密码', },
                        { type: 'string',min:6, message: '密码长度不能小于6位',  }
                    ]
                }
            }
        },
        methods: {
            handleSubmit() {
                if(this.isValidate){
                    return false;
                }
                this.$refs.formItem.validate((valid) => {
                    if (valid) {
                        this.login()
                    }
                });
            },
            async login(){
                this.isValidate = true;
                const loadings = this.$Message.loading({
                    content: '用户信息验证中...',
                    duration: 0
                });
                try {
                    let res = await post('/account/sign-in',{
                        username: this.formItem.account,
                        password: this.formItem.password
                    })
                    this.$Message.success("登录成功");
                    localStorage.setItem('csrf-token', res.data);
                    this.$store.commit('setToken', res.data);
                    this.$router.push({name: 'home'});
                } catch (error) {
                    this.$throw(error)
                }
                setTimeout(loadings, 0);
                this.isValidate = false;
            }
        }
    }
</script>
<style>
body{
    background-image: url("../../../assets/images/login_bg.png");
    height: 100%;
  }
.login-model{
    width: 600px;
    height:378px;
    margin: 0 auto;
    position:absolute;
    top:50%;
    left:50%;
    transform: translate(-50%, -50%);
    background:url(../../../assets/images/login_boxbg.png) bottom center no-repeat;
    text-align: center;
}
.ivu-card-bordered{
    border:0 !important;
    background: transparent !important;
    color:#fff;
}
.ivu-form-item-content label.title{
    width:100px;
    display: inline-block;
}
.ivu-form-item-content .ivu-input-wrapper{
    width:300px !important;
    display: inline-block;
}
.ivu-form-item-error-tip{
    left:175px;
}
.ivu-card-body .loginTitle{
    font-size: 22px;
    margin-bottom:30px;
    display: inline-block;
}
.ivu-btn-primary{
    margin:0;
}
</style>