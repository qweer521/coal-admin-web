<template>
    <div>
        
        <Modal v-model="show" title="考生信息导入"
            :mask-closable="false"  :closable="false">
             <Form ref="modalForm" :model="data" :rules="ruls"
                    :label-width="110">
                <FormItem label="考点：" prop='applysite'>
                    <Input v-model.trim="data.applysite" type="text"></Input>
                </FormItem>
                <FormItem label="考生信息：" prop='fileName'>
                    <Input v-model.trim="fileName" type="text" disabled class='fileName' :class='fileName ? "noFileName" : ""'></Input>
                    <Upload id='importBtn'
                        ref="upload"
                        multiple
                        name="files"
                        :before-upload="handleUpload"
                        :on-success="uploadSuccess"
                        :on-error="uploadError"
                        :format="['xlsx','xls']"
                        :on-format-error="handleFormatError"
                        action="">
                        <Button id='import'>导入</Button>
                    </Upload>
                    <div class="ivu-form-item-error-tip" v-show='noImportFile'>请导入考生列表</div>
                </FormItem>
            </Form>
            <div slot="footer">
                <Button type="default" :disabled="loading" @click="cancel(false)">取消</Button>
                <Button type="primary" :loading="loading" @click="ok">确定</Button>
            </div>
        </Modal>
    </div>
</template>
<script>
import { post } from '@/libs/axios-cfg'
export default {
    data(){
        return {
            show:true,
            loading:false,
            uploadFile: null,
            fileName:null,
            noImportFile:false,
            data:{
                applysite:'',
                examcreater:this.$store.state.user.userId,
            },
            ruls:{
                applysite: [
                    { required: true, message: "请填写题库名称"},
                ],
            }
        }
    },
    props:{
       
    },
    methods:{
        /**
         * @description 关闭Modal
         * @param reload 是否重新加载数据
         */
        cancel(reload = false) {
            this.$emit("cancel", "examImport", reload);
        },
        ok(){
            let self = this
            if(self.fileName){
                self.noImportFile = false
            }else{
                self.noImportFile = true
            }
            this.$refs.modalForm.validate(valid => {
                if (valid) {
                    if(self.fileName){
                        self.noImportFile =false
                        if (self.uploadFile){
                            self.resetPost()
                        } 
                    }else{
                        self.noImportFile =true
                    }
                    
                }
            });
            
        },
        handleUpload (file){
            this.uploadFile=file,
            this.fileName=file.name
            this.noImportFile = false
            return false
        },
         //上传成功调用
        uploadSuccess(response){
          
        },
        //上传失败调用
        uploadError(error){
          this.$Message.warning(error);
        },
        //文件格式错误调用
        handleFormatError(){
          this.$Message.warning("文件格式不正确,请上传excel格式文件");
        },
        async resetPost(){
            this.loading = true;
            let self = this
            let formdata = new FormData();
            formdata.append('file',self.uploadFile);
            try {
                let res = await post("/exam/site/import?applysite="+this.data.applysite,formdata,
                )
                this.cancel(true)
            } catch (error) {
                this.$throw(error)
            }
            this.loading = false;
        },
    }

}
</script>
<style>
    #importBtn{
    position: absolute;
    bottom: -8px;
    right: -4px;
    }
    .fileName input{
        background: transparent !important;
        cursor: default !important;
    }
     #import{
        color:#fff;
        background: #57a3f3;
    }
    #import:focus,#import:hover,#import:active{
        outline: none;
    }
    .noFileName input{
        /*border-color:#ed4014 !important;*/
    }
</style>
