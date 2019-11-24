<template>
    <div>
        <Card>
            <p slot="title" class="card-title">
                <Icon type="home"></Icon>
                <span>成绩查询</span>
            </p>
            <div>
                <template>
                    <div v-if='!detialModal'>
                        <Search :param="param" @search="onSearch"></search>
                        <Table ref="table"  class="margin-bottom-10"
                            :columns="columns" :loading="setting.loading"  :border="setting.showBorder" :data="data.records"></Table>
                        <Page :total="data.total" class="tr" @on-change="pageChange" :current.sync="data.current" :page-size="data.size"
                        @on-page-size-change="pageSizeChange" show-elevator show-sizer></Page>
                    </div>
                    <div v-else>
                        <Button style='margin-bottom:10px;' type="primary" @click="returnExam"><Icon type="ios-return"></Icon>&nbsp;返回</Button>
                        <testpaper :detialData = 'detialData'></testpaper>
                    </div>
                </template>
            </div>
        </Card>
    </div>
</template>
<script>
    import dayjs from 'dayjs'
    import { post } from '@/libs/axios-cfg'
    import Search from './components/search.vue'
    import Testpaper from './components/testpaper.vue'
    export default {
        data () {
            return {   
                detialModal:false,
                setting:{
                    loading:true,
                    showBorder:true
                },
                param:{
                    applysite:"",
                    starttime:"",
                    endtime:"",
                    username:""
                },
                columns: [
                    {title: '序号',
                    render: (h, params) => {
                        return h('span', params.index + (this.dataFilter.page- 1) * this.dataFilter.pageSize + 1);
                    },
                    sortable: true},
                    {title: '考点信息', key: 'applysite',sortable: true},
                    {title: '考试时间', key: 'applytime',sortable: true},
                    {title: '考生姓名', key: 'username',sortable: true},
                    {title: '考生成绩', key: 'examrealscore',sortable: true},
                    {
                        title: '试卷查询',
                        key: 'action',
                        width: 260,
                        align: 'center',
                        render: (h, params) => {
                            return h('div', [
                                h('Button', {
                                    props: {type: 'error',size: 'small'},
                                    on:{
                                        click:()=>{
                                            this.detialObject = {
                                                obj:params.row,
                                                index:params.index
                                            }
                                            this.detialModal = true
                                            this.examDtial()
                                        }
                                    }
                                }, '试卷情况')
                            ]);
                        }
                    }
                ],
                data: {},
                dataFilter:{
                    page:1,
                    pageSize:10
                },
                detialData:{},
                detialObject:null,
            }
        },
        components:{
            Search,Testpaper
        },
        created(){
            this.getData();
        },
        methods:{
            /**
             * @description 返回成绩列表
             */
            returnExam (){
                this.detialModal = false;
            },
            /**
             * @description 搜索
             */
            onSearch(param){
                this.dataFilter.applysite = param.applysite
                this.dataFilter.starttime = param.starttime
                this.dataFilter.endtime = param.endtime
                this.dataFilter.serialno = param.serialno
                this.dataFilter.username = param.username
                this.dataFilter.idno = param.idno
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
             * @description 试卷详情
             */
            async examDtial(){
                if(this.detialObject==null){
                    this.$Message.warning("试卷详情对象为空，无法继续执行！");
                    return false;
                }
                this.setting.loading = true;
                try {
                    let obj = {
                        page:this.dataFilter.page,
                        pageSize:this.dataFilter.pageSize,
                        asc: true,
                        serialno: this.detialObject.obj.serialno
                    }
                    let res = await post('/exam/manager/result/querydetail',obj)
                    this.detialData = res.data

                } catch (error) {
                    this.$throw(error)
                }
                this.setting.loading = false;
            },
            /**
             * @description 获取用户列表
             */
            async getData(){
                this.setting.loading = true;
                try {
                    let res = await post('/exam/manager/result/list',{
                        page:this.dataFilter.page,
                        pageSize:this.dataFilter.pageSize,
                        applyBy: "",
                        asc: true,
                        username: this.dataFilter.username,
                        applysite:this.dataFilter.applysite,
                        starttime:this.dataFilter.starttime,
                        endtime:this.dataFilter.endtime,
                        serialno: ""
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