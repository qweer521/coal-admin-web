<template>
  <div class='examination'>
        <div v-for="(value,key,index) in detialData" class="examinationInfo" :key="index"  v-if='value.length>0'>
            <div class='selection'>
                <span v-if="key == 'judgeSelections'">判断题</span>
                <span v-if="key == 'simpleSelections'">单选题</span>
                <span v-if="key == 'multipleSelections'">多选题</span>
            </div>
            <ul :class='key'>
                <li class='selectionItem' v-for="(val,ind) in value">
                    <span class='questionTitle'>{{ind+1}}、{{val.title}}</span>
                    <div  class='itemContentDiv' v-for="(opitem,opindex) in val.options">
                        <span class='optionBtn' :class="{'currentRadio':val.examrealanswer && val.examrealanswer.indexOf(opitem.no)!=-1,'checkBox':val.type == '3'}"></span>
                        <span v-if='val.type!=1' class='option'>{{opitem.no}}</span>
                        <span class='optionText'>{{opitem.content}}</span>
                    </div>
                    <div class='result'>
                        <span>正确答案: </span><span>{{val.examexpectanswer|xpectanswerFormat}} ,</span>
                        <span>您的答案: </span><span>{{val.examrealanswer|realanswerFormat}}</span>
                        <span class='correct' v-if='sort(val)'>回答正确</span>
                        <span class='error' v-else>回答错误</span>
                    </div>
                </li>
            </ul>
        </div> 
  </div>
</template>
<script>
 //status 0 未开始 1正在答题 2已答题 3答疑
  export default {
    data() {
      return {
        showDialog:false,
        }
    },
    created () {
      var self = this
      self.render()
    },
    filters: {
        realanswerFormat: function (value) {
            if(value){
                return value.replace(/,/g, "")
            }else{
                return ""
            }
        },
        xpectanswerFormat: function (value) {
            if(value){
                return value.replace(/,/g, "")
            }else{
                return ""
            }
        }
    },
    props: {
    detialData: {
      type: Object,
      default: { }
    }
  },
    computed:{
        
    },
    watch:{
        detialData(){
            this.render()
        }
    },
    methods: {
        sort(val){
            if(val.examrealanswer && val.examexpectanswer){
                let str1=val.examrealanswer.toLowerCase().split("").sort().join("")
                let str2=val.examexpectanswer.toLowerCase().split("").sort().join("")
                if(str1 == str2){
                    return true
                }else{
                    return false
                }
            }else{
                return false
            }
            
        },
      render(){
          let self = this
          var list = ["A","B","C","D","E","F","G"]
          for(var name in self.detialData){
            if(self.detialData[name].length>0){
                for(var i=0;i<self.detialData[name].length;i++){
                    if(name == "judgeSelections"){
                        self.detialData[name][i]['type'] = "1"
                    }else if(name == "simpleSelections"){
                        self.detialData[name][i]['type'] = "2"
                    }else if(name == "multipleSelections"){
                        self.detialData[name][i]['type'] = "3"
                    }
                    if(self.detialData[name].options && self.detialData[name].options.length>0){
                        for(var j = 0;j<self.detialData[name].options.length;j++){
                            self.detialData[name].options[j]['option'] = list[j]
                        }
                    }else{
                        for(var f=0;f<self.detialData[name].length;f++){
                            self.detialData[name][f]['options'] = [
                                {
                                    content: "是",
                                    no: "T",
                                    option:"T",
                                },
                                {
                                    content: "否",
                                    no: "F",
                                    option:"F",
                                },
                            ]
                        }
                        
                    }
                }
            }
          }
      },
      cancel(){
          let self = this
          self.showDialog = false
      },
    }
  }
</script>
<style>
::-webkit-scrollbar {/*滚动条整体样式*/
        width: 10px;     /*高宽分别对应横竖滚动条的尺寸*/
        height: 1px;
    }
::-webkit-scrollbar-thumb {/*滚动条里面小方块*/
        border-radius: 10px;
         -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.2);
        background: #535353;
    }
::-webkit-scrollbar-track {/*滚动条里面轨道*/
        -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.2);
        border-radius: 10px;
    }
.examinationInfo{
    border:1px solid #dbdbdb;
    margin-bottom:14px;
}
.examination li{
    list-style: none;
    padding-left:20px;
    border-top:1px solid #dbdbdb;
}
.selection{
    height:40px;
    line-height:40px;
    background: #e8f2ff;
    padding-left:20px;
}
.selection span{
    font-size: 14px;
    font-weight: bold;
    color:#404855;
}
.questionTitle{
    padding:15px 20px 20px 0;
    font-size:14px;
    color:#333;
    display: inline-block;
}
.RadioBtn,.optionBtn{
    display: inline-block;
    width: 16px;
    height: 16px;
    vertical-align: unset;
    position: relative;
}
.optionBtn{
    display: inline-block;
    width: 16px;
    height: 16px;
    background:url(../../../../assets/images/radio_default.png) center center no-repeat;
    vertical-align: middle;
}
.optionBtn.checkBox{
    background:url(../../../../assets/images/checkbox_default.png) center center no-repeat;
}
.currentRadio.optionBtn{
    background:url(../../../../assets/images/radio_selected_gray.png) center center no-repeat;
}
.currentRadio.checkBox.optionBtn{
    background:url(../../../../assets/images/checkbox_selected_gray.png) center center no-repeat;
} 
.itemContentDiv{
    padding-bottom:10px;
}
.optionText{
    padding-left: 6px;
    font-size: 14px;
    color:#666;
}
.option{
    padding-left:6px;
}
.result{
    padding-bottom:15px;
}
.result span{
    font-size: 14px;
    color:#333;
}
.result .correct{
    color:#08c91a;
}
.result .error{
    color:#ff3a3a;
}
.correct,.error{
    padding-left:30px;
}
</style>
