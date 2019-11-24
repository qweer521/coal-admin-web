<template>
    <div>
        <Card>
            <p slot="title" class="card-title">
                <Icon type="home"></Icon>
                <span>考试申报</span>
            </p>
            <div>
                <template>
                    <Row>
                        <Col span="15">
                            <Button type="info" @click="openAddModal"><Icon type="md-add"></Icon>&nbsp;考生信息导入</Button>
                            <Button type="primary" @click="exportData"><Icon type="ios-download-outline"></Icon>&nbsp;模板下载</Button>
                        </Col>
                        <Col span="9">
                            <Input v-model="dataFilter.param" placeholder="请输入您想要搜索的内容..." class="margin-bottom-10">
                                <Button slot="append" icon="ios-search"  @click='search'></Button>
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
        <Modal v-model="applicationModal" width="360">
            <p slot="header" style="color:#f60;text-align:center">
                <Icon type="information-circled"></Icon>
                <span>提示</span>
            </p>
            <div style="text-align:center">
                <p>是否申报考试？</p>
            </div>
            <div slot="footer">
                <Button type="error" size="large" long :loading="setting.loading" @click="applicationExamination">确认申报</Button>
            </div>
        </Modal>
        <ExamImport v-if="examImportModal" :roles="roles" @cancel="onModalCancel"/>
    </div>
</template>
<script>
    import dayjs from 'dayjs'
    import { post } from '@/libs/axios-cfg'
    import ExamImport from './components/examimport.vue'
    export default {
        data () {
            return {
                examImportModal:false,
                selections:[],
                applicationModal:false,
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
                     {title: '部门名称', key: 'deptname',sortable: true},
                    {title: '考点', key: 'applysite',sortable: true},
                    {title: '申报人', key: 'applyby',sortable: true},
                    {title: '考试人数', key: 'applycount',sortable: true},
                    {
                        title: '考试日期',
                        key: 'createDate',
                        render:(h,params)=>{
                            var vm = this;
                            return h("div", [
                                h('DatePicker', {
                                props: {
                                        type: 'date',
                                        format: 'yyyy-MM-dd',
                                        placeholder: '选择日期',
                                },
                                attrs: {
                                        disabled:vm.data.records[params.index].applystatus != -1,
                                    },
                                style: {
                                        marginRight: '5px'
                                },
                                on: {
                                        'on-change': (v1, v2) => {
                                        params.row.createDate = v1;
                                        vm.datalist[params.index] = params.row;
                                }
                                }
                                })
                            ]);
                        },
                        sortable: true
                    },
                    {
                        title: '申报考试',
                        key: 'action',
                        width: 260,
                        align: 'center',
                        render: (h, params) => {
                             return h('div', [
                                    h('Button', {
                                    props: {
                                        type: 'primary',
                                        size: 'small'
                                    },
                                    attrs: {
                                        disabled:this.data.records[params.index].applystatus == -1 ? false : true,
                                    },
                                    on: {
                                        click: () => {
                                            this.applicationObject = {
                                                obj:params.row,
                                                index:params.index
                                            }
                                            if(this.applicationObject.obj.createDate){
                                                this.applicationModal = true;
                                            }else{
                                                this.$Message.warning("请先选择考试日期");
                                                return false;
                                            }
                                        },
                                    },
                                    }, 
                                    this.data.records[params.index].applystatus == -1 ? '申报考试' : (this.data.records[params.index].applystatus == 0 ? "已申报" : (this.data.records[params.index].applystatus == 1 ? "已确认" :"已完成"))),
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
                applicationObject:null,
                roles:null
            }
        },
        components:{
            ExamImport
        },
        created(){
            //applystatus ：-1未申请，0已申请，1已确认
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
             * @description 申报考试
             */
            async applicationExamination(){
                    this.applicationModal = false;
                    if(this.applicationObject==null){
                        this.$Message.warning("申报考试对象为空，无法继续执行！");
                        return false;
                    }
                    this.setting.loading = true;
                    try {
                        let obj = {
                            serialno:this.applicationObject.obj.serialno,
                            applytime:this.applicationObject.obj.createDate,
                        }
                        let res = await post('/exam/site/apply',obj)
                        this.$Message.success("申报考试成功");
                        this.getData()
                    } catch (error) {
                        this.$throw(error)
                    }
                    this.setting.loading = false;
            },
            /**
             * @description 获取考试申报管理列表
             */
            async getData(){
                this.setting.loading = true;
                try {
                    let res = await post('/exam/site/apply/list',{
                        page:this.dataFilter.page,
                        pageSize:this.dataFilter.pageSize,
                        asc: true,
                        queryCondition:this.dataFilter.param,
                    })
                    this.data = res.data;
                } catch (error) {
                    this.$throw(error)
                }
                this.setting.loading = false;
            },
             /**
             * @description 打开模态窗口
             */
            openAddModal(){
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
             * @description 导出表格CSV
             */
            exportData(){
                window.open('http://221.11.24.244:18082/exam/site/export', '_self');
            }
        }
    }
</script>