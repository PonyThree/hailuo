<template>
  <div id="walletDetails">
    <el-row>
        <el-col :span="24">
            <div class="info">
                <div class="header"><p>提现审核表</p></div>
                <el-row class="infoDetails">
                    <el-col :span="8">
                        <div class="infoDetailsdes">项目名称：{{ form.projectName }}</div>
                    </el-col>
                    <el-col :span="8">
                        <div class="infoDetailsdes">申请人：{{ form.applicant }}</div>
                    </el-col>
                    <el-col :span="8">
                        <div class="infoDetailsdes">联系电话：{{ form.applicantPhone }}</div>
                    </el-col>
                </el-row>
                <el-row class="infoDetails">
                    <el-col :span="8">
                        <div class="infoDetailsdes">可提现金额（元）：<span class="fontRed">{{ form.canOutMoney }}</span></div>
                    </el-col>
                    <el-col :span="8">
                        <div class="infoDetailsdes">申请提现金额（元）：<span class="fontRed">{{ form.outMoney }}</span></div>
                    </el-col>
                    <el-col :span="8">
                        <div class="infoDetailsdes">到账地址：{{ form.accountName }}</div>
                    </el-col>
                </el-row>
                <el-row class="infoDetails">
                    <el-col :span="24">
                        <div class="infoRemarks">备注：{{ form.remarks }}</div>
                    </el-col>
                </el-row>
            </div>
        </el-col>
    </el-row>
    <!-- 0:待审核 -->
    <el-row class="walletButton" v-if="this.form.status === 0">
        <el-button type="primary" @click="handClickDetermine">确定</el-button>
        <el-button @click="handClickReject">驳回</el-button>
        <el-button @click="handClickCancel">取消</el-button>
    </el-row>
    <!-- 已驳回2 -->
    <el-row class="walletButton" v-if="this.form.status === 2">
        <el-button type="info" disabled>已驳回</el-button>
        <el-button @click="handClickModify">修改</el-button>
    </el-row>
    <!-- 已审核 1 -->
    <el-row class="walletButton" v-if="this.form.status === 1">
        <el-button type="primary" disabled>已审核</el-button>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'walletDetails',
  data () {
    return {
        id:this.$route.query.id,
        form:{
            projectName:'',
            applicant:'',
            applicantPhone:'',
            canOutMoney:'',
            outMoney:'',
            accountName:'',
            remarks:'',
            status: ''
        }
    }
  },
  created(){
    //   this.renderData();
  },
  mounted(){
      this.renderData();
  },
  methods: {
        //   数据加载
        renderData(){
            // var id=this.$route.query.id;
            // console.log(id);
            this.$axios.get(request.testUrl+"/finance/auth2/applyMoney/selOneData",{
                params:{
                    id:this.id
                }
            }).then(res=>{
                // console.log(res.data.data);
                this.form={};
                // console.log(this.form);
                this.form=res.data.data;
            })
        },
        //确定通过 status=1
        handClickDetermine () {
            this.$confirm('是否确定审核通过?', '审核状态', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                
                // this.status = 1
                var params=new FormData();
                params.append("id",this.id);
                params.append("status",1);
                this.$axios.post(request.testUrl+"/finance/auth2/applyMoney/updApplyStatus",params).then(res=>{
                    // console.log(res.data);
                    if(res.data.code==0){
                        this.form.status=1;
                        this.$message({
                            type: 'success',
                            message: '审核通过!'
                        });
                    }
                })
            }).catch(() => {
                this.$message({
                    type: 'warning',
                    message: '取消审核'
                });      
            });
        },
        //驳回 status=2
        handClickReject () {
            this.$confirm('是否驳回审核?', '审核状态', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                //已驳回 2
                var params=new FormData();
                params.append("id",this.id);
                params.append("status",2);
                this.$axios.post(request.testUrl+"/finance/auth2/applyMoney/updApplyStatus",params).then(res=>{
                    // console.log(res.data);
                    if(res.data.code==0){
                        this.form.status=2;
                        this.$message({
                            type: 'success',
                            message: '已驳回!'
                        });
                    }
                })
            }).catch(() => {
                this.$message({
                    type: 'warning',
                    message: '取消驳回'
                });      
            });
        },
        //已驳回状态 修改为已审核
        handClickModify () {
            this.$confirm('是否修改?', '审核状态', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                var params=new FormData();
                params.append("id",this.id);
                params.append("status",1);
                this.$axios.post(request.testUrl+"/finance/auth2/applyMoney/updApplyStatus",params).then(res=>{
                    // console.log(res.data);
                    if(res.data.code==0){
                        this.form.status=1;
                        this.$message({
                            type: 'success',
                            message: '修改成功!'
                        });
                    }
                })
            }).catch(() => {
                this.$message({
                    type: 'warning',
                    message: '取消修改'
                });      
            });
        },
        //取消
        handClickCancel () {
            this.$router.go(-1)
        }
    }
}
</script>

<style scoped>
    .info {
        width: 100%;
        height: auto;
        border: 1px solid #199ED8;
    }
    .header {
        width: 100%;
        height: 49px;
        line-height: 49px;
        color: #fff;
        background-color: #199ED8;
    }
    .header p {
        padding-left: 20px;
    }
    .fontRed {
        color: red;
    }
    .infoDetails {
        background-color: #fff;
        height: 80px;
        line-height: 80px;
        text-align: center;
        font-size: 14px;
    }
    .stepsPadding {
        padding: 50px;
    }
    .infoDetailsdes {
        width: 70%;
        margin: 0 auto;
        text-align: left;
    }
    .infoRemarks {
        width: 90%;
        margin: 0 auto;
        text-align: left;
    }
    .walletButton {
        text-align: center;
    }
    .walletButton button {
        padding: 12px 30px;
    }
    #walletDetails >>> .is-text {
        width: 18px;
        height: 18px;
        margin-left: 3px;
        background: #c0c4cc;
    }
    #walletDetails >>> .el-row {
        padding-bottom: 40px;
    }
    #walletDetails >>> .el-button--primary {
        color: #fff;
        background-color: #409EFF;
        border-color: #409EFF;
    }
</style>
