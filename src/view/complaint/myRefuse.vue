<template>
	<el-card class="box-card">
    <div slot="header" class="clearfix">
      <span class="title">驳回</span>
    </div>
        <el-form ref="form" :model="form" :rules="rules" label-width="100px">
          <el-form-item label="驳回凭证：" prop="file_url">
            <image-upload data-type="string" :num-limit="1" :size-limit="2*1024*1024" v-model="form.file_url"></image-upload>
          </el-form-item>
        </el-form>
      <div style="float: right; margin-top: 25px; margin-bottom: 20px">
        <el-button type="primary" @click="onSubmit">保存</el-button>
        <el-button @click="reback">取消</el-button>   
      </div>
  </el-card>
</template>

<style scoped>
  .title{
    font-size:20px;
	}
  .operation{
    float: right;
  }
</style>
<style lang="less">
 .avatar-uploader .el-upload {
    border: 1px dashed #ccc;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }
  .el-collapse-item__header {
    font-size: 15px;
  }
	.main .single-page-con {
			background: #fff;
		}
</style>
<script>
import ImageUpload from "../product/components/uploadImg"
import {upload} from '../../api/productManage';
import {saveRefund} from '../../api/complaint';
  export default {
    components: {ImageUpload},
      data () {
        return {
          form: {
            file_url: ''
        },
          file_url: '',
           rules: {
            file_url: [
              { required: true, message: '最多可上传一张驳回凭证', trigger: 'blur' }
            ],
          }
        }
		},
		methods: {
      //返回上一界面
			reback(){
				this.$router.go(-1);
      },
      //保存
      onSubmit() {
        this.$refs.form.validate((valid) => {
          // console.log(this.form)
          // if (valid) {
          //   saveRefund({
          //     id:this.$route.query.id,
          //     file_url: this.form.file_url
          //   })
          //   .then(res => {
          //       if(res.data.code == 200) {
          //         this.$message.success(`${res.data.message}`)
          //       }else {
          //         this.$message.error(`${res.data.message}`)
          //         return false;
          //       }
          //     })
          //     .catch(err => {
          //       this.$message.error(`${err.message}`, 'ERROR!')
          //     }) 
          // }else{
          //   return false;
          // }
        });
      }
    }
  }
</script>
