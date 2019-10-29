<style lang="less">
    @import '../../styles/common.less';
    .search {
      float: left;
      margin-left: 40px;
      margin-top: 15px;
    }
    .el-table th {
      background:#f0f2f5;
    }
    .ivu-row {
      margin-left: -40px;
    }
    a {
      color: #606266;
    }
</style>
<template>
  <div id="app">
		<Card>
      <Row>
        <div class="demo-input-suffix">
          <div class="search">
            <el-input placeholder="请输入手机号" style="width: 180px" v-model="mobile_cnd" suffix-icon="el-icon-search" clearable />
          </div>
          <div class="search">
            <el-input placeholder="请输入身份证号" style="width: 180px" v-model="id_card_cnd" suffix-icon="el-icon-search" clearable />
          </div>
          <div class="search">
            <el-input placeholder="请输入银行卡号" style="width: 180px" v-model="bank_card_cnd" suffix-icon="el-icon-search" clearable />
          </div>
          <div class="search">
            <el-input placeholder="请输入订单号" style="width: 180px" v-model="request_no_cnd" suffix-icon="el-icon-search" clearable />
          </div>
          <div class="search">
					  <el-button @click="handleSearch" type="primary" size="small" style="margin-left: 20px">查询</el-button>
          </div>
        </div>
      </Row>
      <div style="margin-top: 20px">
        <el-table
          v-loading="loading"
          :data="eData"
          border
          style="width: 100%">
          <el-table-column
            fixed
            align="center"
            label="序号"
            type="index"
            width="50">
          </el-table-column>
					<el-table-column
            fixed
            prop="request_no"
            sortable
            align="center"
            label="订单号"
            width="210">
          </el-table-column>
          <el-table-column
            fixed
            prop="mobile"
            sortable
            align="center"
            label="手机号"
            width="150">
          </el-table-column>
          <el-table-column
            prop="id_card"
            sortable
            align="center"
            label="身份证号"
            width="170">
          </el-table-column>
          <el-table-column
            :formatter="dateFormat"
            prop="create_time"
            sortable
            align="center"
            label="注册时间"
            width="170">
          </el-table-column>
          <el-table-column
            prop="source"
            label="来源"
            sortable
            align="center"
            width="120">
          </el-table-column>
          <el-table-column
            prop="user_name"
            label="姓名"
            sortable
            align="center"
            width="110">
          </el-table-column>
           <el-table-column
            prop="bank_card"
            label="银行卡号"
            sortable
            align="center"
            width="180">
          </el-table-column>
           <el-table-column
            prop="user_auth"
            label="认证状态"
            sortable
            align="center"
            width="110">
          </el-table-column>
          <el-table-column
            prop="pay_amt"
            label="已支付金额"
            sortable
            align="center"
            width="120">
          </el-table-column>
					<el-table-column
            prop="amt"
            label="迅付金额"
            sortable
            align="center"
            width="110">
          </el-table-column>
           <el-table-column
            prop="status"
            label="状态"
            sortable
            align="center"
            width="80">
             <template slot-scope="scope">
               {{scope.row.status == "0" ? '正常' : scope.row.status == "1" ? '禁用' : '未知'}}
             </template>
          </el-table-column>
           <el-table-column
            prop="remark"
            label="备注"
            sortable
            align="center"
            width="180">
          </el-table-column>
          <el-table-column
            fixed="right"
            label="操作"
            sortable
            align="center"
            width="160">
            <template slot-scope="scope">
              <el-button @click="handleView(scope.row)" size="small"><router-link :to="{path:'/complaint/emailReback/eRebackInfo',query:{ id:scope.row.id,view: 1 }}" class="around"  style="color: #606266">查看</router-link></el-button>
							<el-button @click="handleReback(scope.row)" size="small" v-if="scope.row.ua != 2 || scope.row.ua != 3">退款</el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-dialog title="退款" :visible.sync="dialogRebackVisible" :append-to-body='true' width="35%" :before-close="closeDiv"  @close="onDialogClose()">
          <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="退款金额" props="money">
              <el-input v-model="form.money" :placeholder='"退款金额应小于"+form.pay_amt+"元"'></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="dialogRebackVisible = false">取 消</el-button>
            <el-button type="primary" @click="handleSave('form')">确 定</el-button>
          </div>
        </el-dialog>
      </div>
			<el-pagination
        style="padding-top: 30px"
        background
        class="pagination"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="currentPage"
        :page-sizes="[5,10,20,30,40]"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total">
      </el-pagination>
		</Card>
  </div>
</template>
<style scoped>
  .paging{
    float:right;
    margin-top:30px;
	}
	.table{
		margin-top: 30px;
	}
</style>
<style lang="less">
	.main .single-page-con {
			background: #fff;
		}
</style>
<script>
import * as table from './data/table';
import {mailRefundList,thirdPay} from '../../api/complaint';
  export default {
      data () {
        return {
          loading: true,
          mobile_cnd: '',
          id_card_cnd: '',
          bank_card_cnd: '',
          request_no_cnd: '',
          eData: [],
          dialogRebackVisible: false,
          formLabelWidth: '120px',
          // 初始化信息总条数
          pageSize:5,
          currentPage: 1,
          total: 10,
          form: {
            money: ''
          }
        }
    },
    methods:{
      dateFormat (row, column) {
        if(row.create_time == null) {
        }else {
          var dateee = new Date(row.create_time).toJSON();
          return new Date(+new Date(dateee) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '')
        }
      },
      //获取列表
      getList(){
        mailRefundList({pageSize: this.pageSize,pageNum: this.currentPage,mobile_cnd: this.mobile_cnd,id_card_cnd: this.id_card_cnd,
        bank_card_cnd: this.bank_card_cnd,request_no_cnd: this.request_no_cnd,
        }).then(res=>{
           if(res.data.code == 200) {
             this.eData = res.data.data.list;
             this.total = res.data.data.total;
             this.loading = false;
           }
        })
      },
      //查询
      handleSearch(){
        mailRefundList({pageSize: this.pageSize,pageNum: this.currentPage,mobile_cnd: this.mobile_cnd,id_card_cnd: this.id_card_cnd,
        bank_card_cnd: this.bank_card_cnd,request_no_cnd: this.request_no_cnd,
        }).then(res=>{
           if(res.data.code == 200) {
             this.eData = res.data.data.list;
             this.total = res.data.data.total;
             this.loading = false;
           }
        })
      },
      //点击页码
      handleSizeChange: function (val) {
        this.pageSize = val;
        this.getList(val,this.currentPage);
      },
      handleCurrentChange: function (val) {
        this.currentPage = val;
        this.getList(this.pageSize,val);
      },
      handleReback(row) {
        let _row = row;
        this.form = Object.assign({}, _row)
        this.dialogRebackVisible = true;
      },
      handleSave(option) {
        // console.log(this.dataForm.roleName)
        //表单验证
        this.$refs[option].validate((valid) => {
          if (valid) {
            this.dialogRebackVisible = false;
            this.$refs.form.resetFields();
            //退款接口传值
            thirdPay({
              id: this.form.id,
              money: this.form.money,
              status: "1",
              type: "1",
              })
              .then(res => {
                if(res.data.code == 200) {
                  this.getList(this.pageSize,this.currentPage);
                  this.$message.success(`${res.data.message}`)
                }else {
                  this.$message.error(`${res.data.message}`)
                  return false;
                }
              })
              .catch(err => {
                this.$message.error(`${err.message}`, 'ERROR!')
              })
          }else{
            return false;
          }
        });
      },
      closeDiv(){
        this.dialogRebackVisible = false;
      },
      // 关闭dialog对话框
      onDialogClose() {
        this.dialogRebackVisible = false;
      },
      handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
      },
      handleView(row) {
        //点击取消后列表页面刷新
        // window.location.href = '/complaint/emailReback/eRebackInfo?id='+row.id
        //点击取消后列表页面不刷新，且接口报undefined
        this.$router.push({ path:'/complaint/emailReback/eRebackInfo?id='+row.id+'&view='+1  })
      },
      // handleView() {
      //   this.$router.push({ path:'/complaint/emailReback/eRebackInfo'  })
      // }
    },
    mounted () {
      this.getList();
    },
    created(){
      this.getList();
    },
    watch: {
      '$route' () {
        this.getList();
        // console.log(this.$route.path);
      }
    }
  }
</script>
