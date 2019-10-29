<template>
  <div id="app">
		<Card>
      <Row>
        <div class="demo-input-suffix">
          <div class="searchc">
            <el-input placeholder="请输入渠道名" style="width: 180px" v-model="name_cnd" suffix-icon="el-icon-search" clearable />
          </div>
          <div class="searchc" style="float: right">
            <el-button  @click="handleSearch" type="primary" size="small" style="margin-left: 20px">查询</el-button>
            <!-- <el-button @click="downloadExcel" type="primary" size="small" style="margin-left: 20px">导出</el-button> -->
          </div>
          <!-- <el-button @click="handleView" type="primary" size="small" style="margin-left: 20px">新增客户</el-button> -->
        </div>
      </Row>
      <div style="margin-top: 20px">
        <el-table
          v-loading="loading"
          :data="payMoney"
          border
          style="width: 100%; align: center">
          <el-table-column
            label="序号"
            type="index"
						align="center"
            width="100">
          </el-table-column>
          <el-table-column
            prop="name"
            sortable
						align="center"
            label="渠道名">
          </el-table-column>
          <el-table-column
            prop="pay_amt"
            sortable
						align="center"
            label="已支付总金额">
          </el-table-column>
        </el-table>
      </div>
			<el-pagination
        style="padding-top: 30px"
        background
        class="pagination"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="pageNum"
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
    @import '../../styles/common.less';
    // @import './components/table.less';
    .main .single-page-con {
			background: #fff;
    }
    // .el-input__inner {
    //   height: 32px;
    //   line-height: 32px;
    // }
    .searchc {
      float: left;
      margin-left: 30px;
      margin-top: 15px;
    }
    .el-table th {
      background:#f0f2f5;
    }
    a{
      color: #606266;
      font-size: 12px;
    }
    a:hover{
      color: #409eff;
      font-size: 12px;
    }
    .switchStyle .el-switch__label {
      position: absolute;
      display: none;
      color: #fff;
    }
    .switchStyle .el-switch__label--left {
      z-index: 1;
      right: -5px;
    }
    .switchStyle .el-switch__label--right {
      z-index: 1;
      left: -3px;
    }
    .switchStyle .el-switch__label.is-active {
      display: block;
    }
   /* .el-switch__label{
      font-size: 13px;
    }
    .el-switch .is-checked .el-switch__core{
      border-color: #409EFF !important;
      background-color: #409EFF !important;
    }*/
    .switchStyle .el-switch__core {
      width: 54px !important;
    }
    .ivu-row {
      margin-left: -30px;
    }
</style>
<script>
import * as table from './data/table';
import {customerList,qryCustomerMoneyAllSys,exportCustomer} from '../../api/customer';
  export default {
      data () {
        return {
          loading: true,
          name_cnd: '',
          payMoney: [],
          // 初始化信息总条数
         // 初始化信息总条数
          pageSize:5,
          pageNum: 1,
          total: 10,
        }
    },
    methods:{
      dateFormat (row, column) {
        if(row.create_time == null) {
        }else {
          var dateee = new Date(row.create_time).toJSON();
          return new Date(+new Date(dateee) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '')
        }
        // var dateee = new Date(row.create_time).toJSON();
        // return new Date(+new Date(dateee) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '')
      },
      //获取客户列表
      getCList(){
        qryCustomerMoneyAllSys({pageSize: this.pageSize,pageNum: this.pageNum,name_cnd: this.name_cnd}).then(res=>{
           if(res.data.code == 200) {
             this.payMoney = res.data.data.list;
             this.total = res.data.data.total;
             this.loading = false;
           }
        })
      },
      //查询
      handleSearch(){
        qryCustomerMoneyAllSys({pageSize: this.pageSize,pageNum: this.pageNum,name_cnd: this.name_cnd}).then(res=>{
           if(res.data.code == 200) {
             this.payMoney = res.data.data.list;
             this.total = res.data.data.total;
             this.loading = false;
           }
        })
      },
      //点击页码
      handleSizeChange: function (val) {
        this.pageSize = val;
        this.getCList(val,this.pageNum);
      },
      handleCurrentChange: function (val) {
        this.pageNum = val;
        this.getCList(this.pageSize,val);
      },
      handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
      },
      //导出接口
      downloadExcel(){
        this.$confirm('确定要导出该表格吗？','提示',{
          confirmButtonText: '确定',
          cancleButtonText: '取消',
          type: 'warning'
        }).then(() => {
          const create_time_FROM_cnd = this.createTime[0];
          const create_time_TO_cnd = this.createTime[1];
          exportCustomer({pageSize: this.pageSize,pageNum: this.pageNum,mobile_cnd: this.mobile_cnd,user_name_cnd: this.user_name_cnd,id_card_cnd: this.id_card_cnd,
        bank_card_cnd: this.bank_card_cnd,user_auth_cnd: this.user_auth_cnd,status_cnd:this.status_cnd,source_cnd: this.source_cnd,
        pay_time_FROM_cnd: this.createTime[0] == undefined ? "" : this.createTime[0],pay_time_TO_cnd:this.createTime[1] == undefined ? "" : this.createTime[1] ,
        create_time_FROM_cnd: this.createTime[0] == undefined ? "" : this.createTime[0],create_time_TO_cnd:this.createTime[1] == undefined ? "" : this.createTime[1] }).then(res=>{
            if(res.data.code == 200) {
              this.exportData = res.data.data;
              this.excelData = this.exportData;
              this.export2Excel();
            }
        })
        }).catch(() => {

        });
      },
      export2Excel(){
        var that = this;
        require.ensure([],() => {
          const {export_json_to_excel } = require('../../excel/export2Excel');
          const tHeader = ['手机号','注册时间','来源','姓名','身份证号','银行卡号','认证状态','已支付金额','状态','备注'];
          const filterVal = ['mobile','create_time','source','user_name','id_card','bank_card','user_auth','pay_amt','status','remark'];
          const list = that.excelData;
          const data = that.formatJson(filterVal,list);
          export_json_to_excel(tHeader,data,'客户列表');
        })
      },
      formatJson(filterVal,jsonData) {
        return jsonData.map(v => filterVal.map(j => v[j]))
      },
    },
    mounted () {
      this.getCList();
    },
    created(){
      this.getCList();
    },
    watch: {
      '$route' () {
        this.getCList();
      }
    }
  }
</script>







