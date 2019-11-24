<template>
    <div>
        <Card>
            <p slot="title" class="card-title">
                <Icon type="home"></Icon>
                <span>考试计划管理</span>
            </p>
            <div>
                <template>
                    <Search :param="param" @search="onSearch"></search>
                    <Table ref="table"  class="margin-bottom-10"
                         :columns="columns" :loading="setting.loading"  :border="setting.showBorder" :data="data.records"></Table>
                    <Page :total="data.total" class="tr" @on-change="pageChange" :current.sync="data.current" :page-size="data.size"
                      @on-page-size-change="pageSizeChange" show-elevator show-sizer></Page>
                </template>
            </div>
        </Card>
        <Modal v-model="approvalModal" width="360">
            <p slot="header" style="color:#f60;text-align:center">
                <Icon type="information-circled"></Icon>
                <span>提示</span>
            </p>
            <div style="text-align:center">
                <p>是否确认审批？</p>
            </div>
            <div slot="footer">
                <Button type="error" size="large" long :loading="setting.loading" @click="approval">确认</Button>
            </div>
        </Modal>
    </div>
</template>
<script>
    import dayjs from 'dayjs'
    import { post } from '@/libs/axios-cfg'
    import Search from './components/search.vue'
    export default {
        data () {
            return {   
                approvalModal:false,
                setting:{
                    loading:true,
                    showBorder:true
                },
                param:{
                    examInfo:"",
                    starttime:"",
                    endtime:""
                },
                columns: [
                    {title: '序号', 
                    render: (h, params) => {
                        return h('span', params.index + (this.dataFilter.page- 1) * this.dataFilter.pageSize + 1);
                    },
                    sortable: true},
                    {title: '考点信息', key: 'applysite',sortable: true},
                    {title: '考试时间', key: 'applytime',sortable: true},
                    {
                        title: '考试申请',
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
                                        disabled:this.data.records[params.index].applystatus == 0 ? false : true,
                                    },
                                    on: {
                                        click: () => {
                                        this.approvalObject = {
                                            obj:params.row,
                                            index:params.index
                                        }
                                        this.approvalModal = true;
                                        },
                                    },
                                    }, this.data.records[params.index].applystatus == 0 ? '未申批' : (this.data.records[params.index].applystatus == 1 ? "已审批" : "已完成")),
                                ]);
                        },
                    }
                ],
                data: {},
                dataFilter:{
                    page:1,
                    pageSize:10,
                    applysite:"",
                    starttime:"",
                    endtime:""

                },
                approvalObject:null,
            }
        },
        components:{
            Search
        },
        created(){
            this.getData();
        },
        methods:{
            /**
             * @description 搜索
             */
            onSearch(param){
                this.dataFilter.applysite = param.applysite
                this.dataFilter.starttime = param.starttime
                this.dataFilter.endtime = param.endtime
                this.getData();
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
            /**
             * @description 审批
             */
            async approval(){
                this.approvalModal = false;
                if(this.approvalObject==null){
                    this.$Message.warning("审批对象为空，无法继续执行！");
                    return false;
                }
                this.setting.loading = true;
                try {
                    let obj = {
                        serialno:this.approvalObject.obj.serialno,
                    }
                    let res = await post('/exam/manager/site/approval',obj)
                    this.$Message.success("审批成功");
                    this.getData()
                } catch (error) {
                    this.$throw(error)
                }
                this.setting.loading = false;
            },
            /**
             * @description 获取考试计划管理列表
             */
            async getData(){
                this.setting.loading = true;
                try {
                    let res = await post('/exam/manager/apply/list',{
                        page:this.dataFilter.page,
                        pageSize:this.dataFilter.pageSize,
                        asc:true,
                        applysite:this.dataFilter.applysite,
                        starttime:this.dataFilter.starttime,
                        endtime:this.dataFilter.endtime
                    })
                    this.data = res.data;
                } catch (error) {
                    this.$throw(error)
                }
                this.setting.loading = false;
            },
        }
    }
</script>