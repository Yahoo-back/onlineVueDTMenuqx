<style lang="less">
    @import '../../styles/common.less';
    // @import './components/table.less';
		// 产品访问数据统计
		.main .single-page-con {
			background: #fff;
    }
    // .el-input__inner {
    //   height: 32px;
    //   line-height: 32px;
    // }
    .search {
      float: left; 
      margin-left: 40px; 
      margin-top: 15px;
    }
    .el-table th {
      background:#f0f2f5;
    }
    .el-dialog {
      width: 40%;
    }
    .el-dialog__title{
      // color:#409eff;
    }
    
</style>
<template>
  <div id="app">
		<Card>
			<!-- <p slot="title">
        <Icon type="help-buoy"></Icon>
        人员管理列表
      </p> -->
      <Row>
        <div class="demo-input-suffix">       
					<!-- 登录账号： -->
          <div class="search">
            <el-input placeholder="请输入登录账号" style="width: 180px" v-model="user_code_cnd" suffix-icon="el-icon-search" clearable />
          </div>
          <!-- 员工姓名： -->
          <div class="search">
            <el-input placeholder="请输入员工姓名" style="width: 180px" v-model="user_name_cnd" suffix-icon="el-icon-search" clearable />
          </div>
					<!-- 身份证号： -->
          <div class="search">
            <el-input placeholder="请输入身份证号" style="width: 180px" v-model="identity_card_cnd" suffix-icon="el-icon-search" clearable />
          </div>
          <!-- 性别： -->
          <div class="search">
            <el-select v-model="sex_cnd" placeholder="请选择性别" style="width:180px">
              <el-option  value="">请选择</el-option>
              <el-option label="男" value="0">男</el-option>
              <el-option label="女" value="1">女</el-option>
            </el-select>
          </div>
          <!-- 状态： -->
          <div class="search">
            <el-select v-model="status_cnd" placeholder="请选择状态" style="width:180px">
              <el-option  value="">请选择状态</el-option>
              <el-option label="在职" value="2">在职</el-option>
              <el-option label="离职" value="4">离职</el-option>
            </el-select>
          </div>
          <div class="search" style="float: right;margin-top: 20px">
            <el-button  @click="handleAdd()" type="primary" size="small" style="margin-left: 20px">新增内部员工</el-button>
             <el-button @click="handleSearch" type="primary" size="small" style="margin-left: 20px">查询</el-button>
          </div>
        </div>
      </Row>
      <div style="margin-top: 20px">
        <el-table
          :data="peopleList"
          border
          style="width: 100%; align: center">
          <el-table-column
            label="序号"
            type="index"
						align="center"
						width="100">
          </el-table-column>
          <el-table-column
            fixed
            prop="USER_CODE"
            sortable
						align="center"
            label="登录账号">
          </el-table-column>
					<el-table-column
            prop="USER_NAME"
            label="员工姓名"
            sortable
						align="center">
          </el-table-column>
					<el-table-column
            fixed
            sortable
						align="center"
            label="性别">
            <template slot-scope="scope">
              {{scope.row.SEX == 0 ? '男' : scope.row.SEX == 1 ? '女' : '未知'}}
            </template>
          </el-table-column>
					<el-table-column
            prop="IDENTITY_CARD"
            label="身份证号"
            sortable
						align="center">
          </el-table-column>
					<el-table-column
            fixed
            prop="STATUS"
            sortable
						align="center"
            label="状态">
            <template slot-scope="scope">
              {{scope.row.STATUS == 2 ? '在职' : scope.row.STATUS == 4 ? '离职' : scope.row.STATUS == 0 ? '离职' : '未知'}}
            </template>
          </el-table-column>
					 <el-table-column
            fixed="right"
            label="操作"
            sortable
            align="center"
            width="320">
            <template slot-scope="scope">
              <!-- 查看界面跳转 -->
              <!-- <el-button @click="handleView" type="text" size="small">查看</el-button> -->
              <el-button @click="handlefp(scope.row)" type="text" size="small" v-if="scope.row.STATUS == 2">分配弹框</el-button>
              <el-dialog append-to-body title="分配角色" :visible.sync="dialogTable" width="900px">
                <el-table ref="roleTable" 
                  :data="roleData" 
                  @selection-change="handleSelectionChange">
                  <el-table-column
                    fixed="left"
                    label="序号"
                    type="index"
                    align="center"
                    width="80px">
                  </el-table-column>
                  <el-table-column
                    fixed="left"
                    prop="roleid"
                    sortable
                    align="center"
                    label="角色ID">
                  </el-table-column>
                  <el-table-column
                    prop="roleName"
                    label="角色名称"
                    sortable
                    align="center">
                  </el-table-column>
                </el-table>
                 <el-pagination 
                  style="padding-top: 30px"
                  background
                  class="pagination"
                  @size-change="handleSizeChange" 
                  @current-change="handleCurrentChange"
                  :current-page.sync="currentPage" 
                  :page-sizes="[5,7,10,20,30,40]" 
                  :page-size="pageSize"
                  layout="total, sizes, prev, pager, next, jumper" 
                  :total="totalr">
                </el-pagination>
                <div style="margin-top: 20px;font-size: 1rem">当前角色</div>
                <el-table
                  :data="userRole"
                  border
                  style="width: 100%; align: center;margin-top: 10px">
                  <el-table-column
                    fixed="left"
                    label="序号"
                    type="index"
                    align="center"
                    width="80px">
                  </el-table-column>
                  <el-table-column
                    prop="roleName"
                    label="角色名称"
                    sortable
                    align="center">
                  </el-table-column>
                </el-table>
                <el-form :model="roleForm" ref="roleForm">
                  <el-form-item prop="userRole">
                    当前角色：
                    <el-checkbox-group v-model="roleForm.userRole" v-for="item in userRole" :key="item.roleid"  @change="changeCheck(item)">
                      <el-checkbox :value="item.roleid" :label="item.roleid">{{item.roleName}}</el-checkbox>
                      <!-- <el-checkbox @change="changeCheck(item)" v-for="item in roleForm.userRole" :v-model="roleForm.roleid"  :label="item.roleid" :key="item.roleid">{{item.roleName}}</el-checkbox> -->
                    </el-checkbox-group>
                    <br/>
                  </el-form-item>
                  <el-form-item prop="roleData">
                    可分配角色：
                    <el-checkbox-group v-model="roleForm.roleData" v-for="i in roleData" :key="i.roleid">
                      <!-- <el-checkbox @change="changeCheck(item)" v-for="item in roleData" :v-model="item.roleid"  :label="item.roleid" :key="item.roleid">{{item.roleName}}</el-checkbox> -->
                      <el-checkbox @change="changeChecks(i)"  :label="i.roleid" :value="i.roleid"  >{{i.roleName}}</el-checkbox>
                    </el-checkbox-group>
                  </el-form-item>
                  <el-form-item>
                    <el-button type="primary" @click="handleSaveRoles(scope.row)">保存</el-button>
                    <el-button @click="handleCancleRole('roleForm')">取消</el-button>
                  </el-form-item>
                </el-form>
                <!-- <div slot="footer" class="dialog-footer">
                  <el-button @click="handleCancleRole('roleForm')">取消</el-button>
                  <el-button @click="handleSaveRole(scope.row)">保存</el-button>
                </div> -->
              </el-dialog>
              <el-button @click="handleLook(scope.$index, scope.row)" type="text" size="small">查看</el-button>
              <el-button @click="handleEdit(scope.$index, scope.row)" type="text" size="small"  v-if="scope.row.STATUS == 2">修改</el-button>
              <el-button @click="handleLeave(scope.row)" type="text" size="small" v-if="scope.row.STATUS == 2">离职</el-button>
              <el-button @click="handleRecover(scope.row)" type="text" size="small" v-if="scope.row.STATUS == 0 || scope.row.STATUS == 4">复职</el-button>
              <el-button @click="handleDistribute(scope.row)" type="text" size="small" v-if="scope.row.STATUS == 2">分配岗位</el-button>
              <el-button @click="handleReset(scope.row)" type="text" size="small" v-if="scope.row.STATUS == 2">重置密码</el-button>
              <el-button type="text" @click="handleDelete(scope.row)" size="small">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <el-pagination 
        style="padding-top: 30px"
        background
        class="pagination"
        @size-change="handleSizeChange" 
        @current-change="handleCurrentChange"
        :current-page.sync="currentPage" 
        :page-sizes="[5,7,10,20,30,40]" 
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper" 
        :total="total">
      </el-pagination>
      <el-dialog :append-to-body='true' :title="dialogTitle" :visible.sync="dialogVisible" @close="onDialogClose()" >
        <el-form ref="dataForm" :model="dataForm" :rules="rules" label-width="100px">
          <el-row>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="姓名：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.userName }}</template>
                <el-input  v-else v-model="dataForm.userName" placeholder="姓名"></el-input>
              </el-form-item>
            </el-col>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="登录账号：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.userCode }}</template>
                <el-input  v-else v-model="dataForm.userCode" placeholder="登录帐号"></el-input>
              </el-form-item>
            </el-col>
          </el-row> 
          <el-row>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">  
              <el-form-item label="性别：" >
                <template v-if="dialogTitle=='查看员工信息'">{{this.sex == "0" ? "男" : dataForm.sex == "1" ? "女" : "未知"}}</template>
                <el-select  v-else v-model="dataForm.sex" placeholder="请选择">
                  <el-option label="男" value="0">男</el-option>
                  <el-option label="女" value="1">女</el-option>
                </el-select>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="身份证：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.identityCard}}</template>
                <el-input v-else v-model="dataForm.identityCard" placeholder="身份证"></el-input>
              </el-form-item>
            </el-col>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="工号：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.jobNum}}</template>
                <el-input v-else v-model="dataForm.jobNum" placeholder="工号"></el-input>
              </el-form-item>
            </el-col>
          </el-row>   
          <el-row>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="学历：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.degree == "0" ? "本科以上" : dataForm.degree == "1" ? "本科" :  dataForm.degree == "2" ? "大专" : dataForm.degree == "3" ? "高中" : dataForm.degree == "4" ? "初中" : "未知" }}</template>
                <el-select  v-else v-model="dataForm.degree" placeholder="请选择">
                  <el-option label="本科以上" value="0">本科以上</el-option>
                  <el-option label="本科" value="1">本科</el-option>
                  <el-option label="大专" value="2">大专</el-option>
                  <el-option label="高中" value="3">高中</el-option>
                  <el-option label="初中" value="4">初中</el-option>
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="电话：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.phone}}</template>
                <el-input v-else v-model="dataForm.phone" placeholder="电话"></el-input>
              </el-form-item>
            </el-col>
          </el-row>  
          <el-row>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="公司邮箱：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.companyEmail}}</template>
                <el-input v-else v-model="dataForm.companyEmail" placeholder="公司邮箱"></el-input>
              </el-form-item>
            </el-col>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="个人邮箱：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.personalEmail}}</template>
                <el-input v-else v-model="dataForm.personalEmail" placeholder="个人邮箱"></el-input>
              </el-form-item>
            </el-col>
          </el-row> 
          <el-row>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="QQ：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.qq}}</template>
                <el-input v-else v-model="dataForm.qq" placeholder="QQ"></el-input>
              </el-form-item>
            </el-col>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="微信：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.weixin}}</template>
                <el-input v-else v-model="dataForm.weixin" placeholder="微信"></el-input>
              </el-form-item>
            </el-col>
          </el-row> 
          <el-row>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="入职时间：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.od == null ? "" : this.od}}</template>
                  <el-date-picker 
                    v-else v-model="dataForm.onjobdate"  
                    type="date" 
                    value-format="yyyy-MM-dd HH:mm:ss" 
                    format="yyyy-MM-dd HH:mm:ss"
                    placeholder="选择日期">
                  </el-date-picker>
              </el-form-item>
            </el-col>
            <el-col :xs="12" :sm="12" :md="10" :lg="12" :xl="12">
              <el-form-item label="所属部门：">
                <template v-if="dialogTitle=='查看员工信息'">{{this.department}}</template>
                <el-input v-else v-model="dataForm.department" placeholder="所属部门"></el-input>
              </el-form-item>
            </el-col>
          </el-row> 
          <el-form-item label="家庭住址：" prop="liveAddr">
            <template v-if="dialogTitle=='查看员工信息'">{{this.liveAddr}}</template>
            <el-input v-else v-model="dataForm.liveAddr" placeholder="家庭地址"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="handleCancle()">取 消</el-button>
          <span @click="onDialogSubmit()" v-if="dialogTitle=='查看员工信息'"></span>
          <el-button type="primary" @click="handleSubmit('dataForm')" v-else>保存</el-button>
        </div>
      </el-dialog>
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
<script>
import {qryUserByLgnId,qryUserList,resetPassword,saveUser,updateUserStatus,qryRoles,qryUserRole,allocationRoles} from '../../api/right';
import * as table from './data/table';
  export default {
      data () {
        return {
            userCode: '',
            userName: '',
            userId: '',
            sex: '',
            degree: '',
            jobNum: '',
            phone: '',
            companyEmail: '',
            personalEmail: '',
            qq: '',
            weixin: '',
            onjobdate: '',
            od: '',
            createDate: '',
            department: '',
            liveAddr: '',
            identityCard: '',
            //modal内嵌表单
            totalr: 10,
            roleData: [],
            userRole: [],
            roleData: {
              roleid: '',
              roleName: '',
              roleCode: '',
            },
            userRole: {
              roleid: '',
              roleName: '',
              roleCode: '',
            },
            roleId_cnd: '',
            roleName_cnd: '',
            roleCode_cnd: '',
            dialogTable: false,
            choseRole: [],
            choseRoleId: '',
            //查看用户已经分配的角色
            roleid: '',
            userRole: [],
          dataForm: {
            userId: '',
            userCode: '',
            userName: '',
            sex: '',
            degree: '',
            jobNum: '',
            phone: '',
            companyEmail: '',
            personalEmail: '',
            qq: '',
            weixin: '',
            onjobdate: '',
            od: '',
            createDate: '',
            department: '',
            liveAddr: '',
            identityCard: '',
          },
          roleForm:{
            // roleid: '',
            // roleName: '',
            roleData: '',
            userRole: ''
          },
          dialogVisible: false,
          dialogTitle: '查看员工信息',
          user_code_cnd: '',
          user_name_cnd: '',
          sex_cnd: '',
          identity_card_cnd: '',
          status_cnd: '',
          // 初始化信息总条数
          pageSize:10,
          currentPage: 1,
          total: 10,
          peopleList: [],
          peopleDetail: '',
          dialogVisibleLeave: false,
          dialogVisibleResume: false,
					dialogVisibleNo: false,
					dialogVisibleSale: false,
          dialogVisibleUp: false,
          dialogVisibleDown: false,
          dialogVisibleDelete: false,
          createTime: '',
          createTime: '',        
          status: '',
          rules: {
            userName: [
              { required: true, message: '请输入员工姓名', trigger: 'blur' },
            ],
            userCode: [
              { required: true, message: '请输入登陆账号', trigger: 'blur' }
            ],
            identityCard: [
              { required: true, message: '请输入身份证号', trigger: 'blur' }
            ],
            phone: [
              { required: true, message: '请输入电话', trigger: 'blur' }
            ]
          }
        }
    },
    methods:{
      //获取员工列表
      getRoleList(){
        qryRoles({pageSize: this.pageSize,pageNum: this.currentPage,roleId_cnd: this.roleId_cnd,roleName_cnd: this.roleName_cnd,roleCode_cnd: this.roleCode_cnd}).then(res=>{
           if(res.data.code == 200) {
             this.roleData = res.data.data.list;
             this.roleForm.roleData = res.data.data.list;
             this.totalr = res.data.data.total;
                this.roleForm.roleData = res.data.data.list;
            // this.roleForm.roleData.map(item=> {
            //   this.roleForm.roleid = item.roleid;
            //   this.roleForm.roleName = item.roleName;
            // })
           }
        })
      },
      //查询
      handleSearch(){
        qryRoles({pageSize: this.pageSize,pageNum: this.currentPage,roleId_cnd: this.roleId_cnd,roleName_cnd: this.roleName_cnd,roleCode_cnd: this.roleCode_cnd}).then(res=>{
           if(res.data.code == 200) {
             this.roleData = res.data.data.list;
             this.total = res.data.data.total;
           }
        })
      },
      //获取员工列表
      getPeopleList(){
        qryUserList({pageSize: this.pageSize,pageNum: this.currentPage,user_code_cnd: this.user_code_cnd,identity_card_cnd: this.identity_card_cnd,user_name_cnd: this.user_name_cnd,
        status_cnd: this.status_cnd,sex_cnd: this.sex_cnd}).then(res=>{
           if(res.data.code == 200) {
             this.peopleList = res.data.data.list;
             this.total = res.data.data.total;
           }
        })
      },
      //查询
      handleSearch(){
        qryUserList({pageSize: this.pageSize,pageNum: this.currentPage,user_code_cnd: this.user_code_cnd,identity_card_cnd: this.identity_card_cnd,user_name_cnd: this.user_name_cnd,
        status_cnd: this.status_cnd,sex_cnd: this.sex_cnd}).then(res=>{
           if(res.data.code == 200) {
             this.peopleList = res.data.data.list;
             this.total = res.data.data.total;
           }
        })
      },
      //点击页码
      handleSizeChange: function (val) {
        this.pageSize = val;
        this.getPeopleList(val,this.currentPage);
      },
      handleCurrentChange: function (val) {
        this.currentPage = val;
        this.getPeopleList(this.pageSize,val);
      },
      //重置密码
      handleReset(row) {
        this.$confirm('确定要重置该员工密码吗?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
          .then(() => {
            resetPassword({id: row.userId})
              .then(res => {
              if(res.data.code == 200) {
              this.$message.success(`${res.data.message}`)
              this.getPeopleList(this.pageSize,this.currentPage);
            }else {
              this.$message.error(`${res.data.message}`)
              return false;
            }
              })  
          }).catch(() => {

        });    
      },
      //离职
      handleLeave(row) {
        this.$confirm('确定给该员工离职吗?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
          .then(() => {
            updateUserStatus({id: row.userId,status: "4"})
              .then(res => {
              if(res.data.code == 200) {
              this.$message.success(`${res.data.message}`)
              this.getPeopleList(this.pageSize,this.currentPage);
            }else {
              this.$message.error(`${res.data.message}`)
              return false;
            }
              })  
          }).catch(() => {
        });    
      },
      //复职
      handleRecover(row) {
        this.$confirm('确定要给该员工复职吗?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
          .then(() => {
            resetPassword({id: row.userId,status: "2"})
              .then(res => {
              if(res.data.code == 200) {
              this.$message.success(`${res.data.message}`)
              this.getPeopleList(this.pageSize,this.currentPage);
            }else {
              this.$message.error(`${res.data.message}`)
              return false;
            }
              })  
          }).catch(() => {
        });    
      },
      //删除
      // handleDelete(row) {
      //   this.$confirm('确定删除该员工吗?', '提示', {
      //     confirmButtonText: '确定',
      //     cancelButtonText: '取消',
      //     type: 'warning'
      //   })
      //     .then(() => {
      //       updateUserStatus({id: row.userId,status: "5"})
      //         .then(res => {
      //         if(res.data.code == 200) {
      //         this.$message.success(`${res.data.message}`)
      //         this.getPeopleList(this.pageSize,this.currentPage);
      //       }else {
      //         this.$message.error(`${res.data.message}`)
      //         return false;
      //       }
      //         })  
      //     }).catch(() => {
      //   });    
      // },
      //保存
      handleSubmit(option) {
        //表单验证
        this.$refs[option].validate((valid) => {
          // console.log(this.dataForm)
          if (valid) {
            this.dialogVisible = false;
            this.$refs.dataForm.resetFields(); 
            //登录接口传值
            saveUser(
              {
              companyEmail: this.dataForm.companyEmail, 
              degree: this.dataForm.degree,
              department: this.dataForm.department,
              identityCard: this.dataForm.identityCard,
              jobNum: this.dataForm.jobNum,
              liveAddr: this.dataForm.liveAddr,
              onjobDate: this.dataForm.onjobdate,
              personalEmail: this.dataForm.personalEmail,
              phone: this.dataForm.phone,
              qq: this.dataForm.qq,
              userCode: this.dataForm.userCode,
              sex: this.dataForm.sex,
              userName: this.dataForm.userName,
              id: this.dataForm.userId,
              weixin: this.dataForm.weixin,
              }
              )
              .then(res => {
                if(res.data.code == 200) {
                  this.getPeopleList(this.pageSize,this.currentPage);
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
        // console.log('submit!');
      },
       //取消按钮
      handleCancle (option) {
        this.$refs[option].validate((valid) => {
          if (valid) {
            this.dialogVisible = false;
            this.$refs.dataForm.resetFields(); 
        // this.dialogVisible = false;
        // this.$refs.dataForm.resetFields(); 
          }
        })
      },
      handleDistribute() {
        this.$router.push({ path:'/rightManage/people/distributionPosts'  })
      },
      //点击编辑弹出对话框dialog
      onDialogClose() {
        this.dataForm.tempRoleIds = []
        this.$refs.dataForm.resetFields()
      },
      handleLook(index, row) {
        this.dialogVisible = true
        this.dialogTitle = '查看员工信息'
        qryUserByLgnId({userId: row.userId}).then(res => {
          if(res.data.code == 200) {
            this.peopleDetail = res.data.data.user;
            this.peopleLogin = res.data.data.login;
            this.userCode = this.peopleLogin.userCode;
            this.userName = this.peopleDetail.userName;
            this.sex = this.peopleDetail.sex;
            this.degree = this.peopleDetail.degree;
            this.jobNum = this.peopleDetail.jobNum;
            this.phone = this.peopleDetail.phone;
            this.companyEmail = this.peopleDetail.companyEmail;
            this.personalEmail = this.peopleDetail.personalEmail;
            this.qq = this.peopleDetail.qq;
            this.weixin = this.peopleDetail.weixin;
            this.onjobdate = this.peopleDetail.onjobdate;
            this.createDate = this.peopleDetail.createDate;
            if(this.onjobdate == null) {
            }else{
              var ct =  new Date(this.onjobdate).toJSON();
              var time = new Date(+new Date(ct) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '') 
              this.od = time;
            }
            this.department = this.peopleDetail.department;
            this.liveAddr = this.peopleDetail.liveAddr;
            this.identityCard = this.peopleDetail.identityCard;
          }
        })
        // this.dataForm.tempRoleIds = []
        // for (let x of Object.keys(this.dataForm)) {
        //   if (
        //     x === 'tempRoleIds' &&
        //     typeof row.roleList === 'object' &&
        //     row.roleList.length > 0
        //   ) {
        //     for (let item of row.roleList) {
        //       this.dataForm.tempRoleIds.push(item.id)
        //     }
        //   } else {
        //     this.dataForm[x] = row[x]
        //     }
        // }
      },
      handleEdit(index,row) {
        this.dialogVisible = true
        this.dialogTitle = '修改员工信息'
        qryUserByLgnId({userId: row.userId}).then(res => {
          if(res.data.code == 200) {
            this.peopleDetail = res.data.data.user;
            this.peopleLogin = res.data.data.login;
            this.dataForm.userCode = this.peopleLogin.userCode;
            this.dataForm.userName = this.peopleDetail.userName;
            this.dataForm.userId = this.peopleDetail.userid;
            this.dataForm.sex = this.peopleDetail.sex;
            this.dataForm.degree = this.peopleDetail.degree;
            this.dataForm.jobNum = this.peopleDetail.jobNum;
            this.dataForm.phone = this.peopleDetail.phone;
            this.dataForm.companyEmail = this.peopleDetail.companyEmail;
            this.dataForm.personalEmail = this.peopleDetail.personalEmail;
            this.dataForm.qq = this.peopleDetail.qq;
            this.dataForm.weixin = this.peopleDetail.weixin;
            this.dataForm.onjobdate = this.peopleDetail.onjobdate;
            if(this.dataForm.onjobdate == null) {
            }else {
              var ct = new Date(this.dataForm.onjobdate).toJSON();
              var time = new Date(+new Date(ct) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '') 
              this.od = time;
              this.dataForm.onjobdate = time;
            }            
            this.dataForm.createDate = this.peopleDetail.createDate;
            this.dataForm.department = this.peopleDetail.department;
            this.dataForm.liveAddr = this.peopleDetail.liveAddr;
            this.dataForm.identityCard = this.peopleDetail.identityCard;
          }
        })
      },
      handleAdd() {
        this.dialogVisible = true
        this.dialogTitle = '新增员工'
      },
      onDialogSubmit() {
          
      },
      //获取分配角色列表
      handlefp(row) {
        this.dialogTable = true;
        qryUserRole({userCode: row.userId}).then(res=>{
          if(res.data.code == 200) {
            this.userRole = res.data.data.list;
            this.roleForm.userRole = res.data.data.list;
            // console.log(this.userRole)
            // this.userRole.forEach(item => {
            // let key = item.roleid
            // this.$set(this.roleForm, key, [])
            // console.log(key)
            // })
            // this.userRole.roleid = res.data.data.list.roleid;
            // this.roleForm.userRole = res.data.data.list;
            // this.roleForm.userRole.map(item=> {
            //   this.roleForm.roleid = item.roleid;
            //   this.roleForm.roleName = item.roleName;
            // })
            // console.log(this.roleForm.userRole)
          }
        })
      },
      //获取分配角色列表内的复选框值
      handleSelectionChange(val) {
        this.choseRole = val;
      },
      //取消按钮
      handleCancleRole(name){
        this.$refs[name].resetFields();
        this.dialogTable = false
        // this.$refs[option].resetFields();
      },
      changeCheck(item) {
        let i = item.roleid;
        this.choseRoleId = item.roleid;
        // console.log(this.choseRoleId)
      },
      changeChecks(i) {
      let item = i.roleid;
      this.choseRoleId = i.roleid;
      // console.log(this.choseRoleId)
		},
      //保存角色分配
      handleSaveRoles(row){
        this.$refs.roleForm.validate((valid) => {
          if(valid) {
            // console.log(this.roleForm)
            // console.log(this.roleForm.roleData)
            //  console.log(this.roleForm.userRole)
            console.log(this.choseRoleId)
          }
        })
        // console.log(row.userId)
        
        // var giveRole = this.choseRole;
        // var roleids = '';
        // giveRole.map((item)=>{
          // roleids=item.roleid;
          // console.log(roleids)
        // })
        // allocationRoles({roleId: row.userId,roleId_cnd: roleids}).then(res=>{
        //   if(res.data.code == 200){

        //   }
        // })
        //  console.log(roleids)
      },
      //保存角色分配
      handleSaveRole(row){
        console.log(row.userId)
        console.log(this.choseRole)
        var giveRole = this.choseRole;
        var roleids = '';
        giveRole.map((item)=>{
          roleids=item.roleid;
          console.log(roleids)
        })
        // allocationRoles({roleId: row.userId,roleId_cnd: roleids}).then(res=>{
        //   if(res.data.code == 200){

        //   }
        // })
         console.log(roleids)
      }
    },
    computed: {
      
    },
    mounted () {
      this.getPeopleList();
      this.getRoleList();
    },
    created(){
    },
    watch: {
      '$route' () {
        this.getPeopleList();
        this.getRoleList();
      }
    }
  }
</script>
