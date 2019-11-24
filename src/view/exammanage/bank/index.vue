<template>
    <div>
        <Card>
            <p slot="title" class="card-title">
                <Icon type="home"></Icon>
                <span>题库管理</span>
            </p>
            <div>
                <template>
                    <Row>
                        <Col span="15">
                            <Button type="info" @click="importExam"><Icon type="md-add"></Icon>&nbsp;题库导入</Button>
                            <Button type="primary" @click="exportData"><Icon type="ios-download-outline"></Icon>&nbsp;模板下载</Button>
                        </Col>
                        <Col span="9">
                            <Input v-model="dataFilter.param" placeholder="请输入您想要搜索的内容..." class="margin-bottom-10">
                                <Button slot="append" icon="ios-search" @click='search'></Button>
                            </Input>
                        </Col>
                    </Row>
                    <Table ref="table"  class="margin-bottom-10"
                         :columns="columns" :loading="setting.loading"  :border="setting.showBorder" :data="data.records"></Table>
                    <Page :total="data.total" class="tr" @on-change="pageChange" :current.sync="data.current" :page-size="data.size"
                      @on-page-size-change="pageSizeChange" show-elevator show-sizer></Page>
                </template>
            </div>
        </Card>
        <Modal v-model="removeModal" width="360">
            <p slot="header" style="color:#f60;text-align:center">
                <Icon type="information-circled"></Icon>
                <span>提示</span>
            </p>
            <div style="text-align:center">
                <p>此操作为不可逆操作，是否确认删除？</p>
            </div>
            <div slot="footer">
                <Button type="error" size="large" long :loading="setting.loading" @click="removeExam">确认删除</Button>
            </div>
        </Modal>
        <ExamImport v-if="examImportModal"  @cancel="onModalCancel"/>
    </div>
</template>
<script>
    import dayjs from 'dayjs'
    import { post,get } from '@/libs/axios-cfg'
    import ExamImport from './components/examimport.vue'
    export default {
        data () {
            return {
                examImportModal:false,
                selections:[],
                removeModal:false,
                setting:{
                    loading:true,
                    showBorder:true
                },
                columns: [
                    {title: '序号', 
                    render: (h, params) => {
                        return h('span', params.index + (this.dataFilter.page- 1) * this.dataFilter.pageSize + 1);
                    },
                    sortable: true},
                    {title: '题库名称', key: 'bankname',sortable: true},
                    {
                        title: '题库导入时间',
                        key: 'createDate',
                        render:(h,params)=>{
                            return h('span',dayjs(params.row.createDate).format('YYYY年MM月DD日 HH:mm:ss'))
                        },
                        sortable: true
                    },
                    {
                        title: '操作',
                        key: 'action',
                        width: 260,
                        align: 'center',
                        render: (h, params) => {
                            return h('div', [
                                h('Button', {
                                    props: {type: 'error',size: 'small'},
                                    on:{
                                        click:()=>{
                                            this.removeObject = {
                                                obj:params.row,
                                                index:params.index
                                            }
                                            this.removeModal = true;
                                        }
                                    }
                                }, '删除')
                            ]);
                        }
                    }
                ],
                data: {},
                dataFilter:{
                    page:1,
                    pageSize:10,
                    param:""
                },
                removeObject:null,
            }
        },
        components:{
            ExamImport
        },
        created(){
            this.getData();
        },
        methods:{
            /**
             * @description 导入题库
             */
            examImport(){
                this.examImportModal = true;
            },
            /**
             * @description 分页更换事件回调
             */
            pageChange(p){
                this.dataFilter.page = p;
                this.getData();
            },
            /**
             * @description 分页每页显示数量改变事件回调
             */
            pageSizeChange(p){
                this.dataFilter.pageSize = p;
                this.getData();
            },
            search () {
                this.getData();
            },
            /**
             * @description 删除用户
             */
            async removeExam(){
                this.removeModal = false;
                if(this.removeObject==null){
                    this.$Message.warning("删除对象为空，无法继续执行！");
                    return false;
                }
                this.setting.loading = true;
                try {
                    let res = await post('/exam/manager/bank/remove/{bankno}',null,{
                        bankno: this.removeObject.obj.bankno
                    })
                    this.$Message.success("删除成功");
                    this.getData()
                } catch (error) {
                    this.$throw(error)
                }
                this.setting.loading = false;
            },
            /**
             * @description 获取题库管理列表
             */
            async getData(){
                this.setting.loading = true;
                try {
                    let res = await post('/exam/manager/bank/list',{
                        page:this.dataFilter.page,
                        pageSize:this.dataFilter.pageSize,
                        asc:true,
                        bankname:this.dataFilter.param,
                    })
                    this.data = res.data;
                } catch (error) {
                    this.$throw(error)
                }
                this.setting.loading = false;
            },
             /**
             * @description 打开导入题库弹框
             */
            importExam(){
                 this.examImportModal = true;
            },
            /**
             * @description 关闭模态窗口
             */
            onModalCancel(data){
                this.examImportModal = false;
                if(data){
                    this.getData()                    
                }
            },
            /**
             * @description 题库模板下载
             */
            exportData(){
                 window.open('http://221.11.24.244:18082/exam/manager/bank/export', '_self');
            }
        }
    }
</script>