<template>
  <div style="width: 100%;height:100%">
    <h3 style="color: cornflowerblue;margin-bottom: -150px;margin-top: 30px">个人信息修改</h3>
    <el-form ref="form" :model="user" label-width="35%" style="width: 50%;margin: auto">
      <el-form-item label="">
        <el-input v-model="user.userId" type="hidden"></el-input>
      </el-form-item>
      <!--<el-form-item label="">-->
        <!--<el-input v-model="user.password" type="hidden"></el-input>-->
      <!--</el-form-item>-->
      <el-form-item label="用户名">
      <el-input v-model="user.username" style="width: 65%;margin-left: -125px"></el-input>
      </el-form-item>
      <el-form-item label="密码">
      <el-input v-model="user.password" type="password" @blur="check()" style="width: 65%;margin-left: -125px" placeholder="请输入新密码"></el-input>
      </el-form-item>
      <el-form-item label="确认密码">
      <el-input v-model="repeatpassword"  type="password" @blur="confirmpassword()" style="width: 65%;margin-left: -125px" placeholder="请确认新密码"></el-input>
      </el-form-item>
      <!--<el-form-item label="确认密码">-->
      <!--<el-input v-model="user.username" style="width: 65%;margin-left: -125px" placeholder="请确认新密码"></el-input>-->
      <!--</el-form-item>-->
      <el-form-item label="邮箱">
      <el-input v-model="user.email" style="width: 65%;margin-left: -125px" ></el-input>
      </el-form-item>
      <el-form-item label="上传头像" style="width: 80%;float: left" >
        <el-upload
          class="upload-file"
          drag
          :action="doUpload"
          :before-upload="beforeUpload">
          <i class="el-icon-upload"></i>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
        </el-upload>
        <!--<el-upload-->
          <!--class="avatar-uploader"-->
          <!--action="api/upload"-->
          <!--:show-file-list="false"-->
          <!--:on-success="handleAvatarSuccess"-->
          <!--:before-upload="beforeAvatarUpload">-->
          <!--<img v-if="user.pic" :src="user.pic"  class="avatar">-->
          <!--<i v-else class="el-icon-plus avatar-uploader-icon"></i>-->
        <!--</el-upload>-->
      </el-form-item>
      <el-form-item style="width: 65%;margin-left: 65px">
        <el-button type="primary" @click="userupdate()">确定</el-button>
        <el-button @click="goback()">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
  import axios from 'axios'
  import ElImage from "../../node_modules/element-ui/packages/image/src/main";
  import ElButton from "../../node_modules/element-ui/packages/button/src/button";
  import ElMenu from "../../node_modules/element-ui/packages/menu/src/menu";
  import ElForm from "../../node_modules/element-ui/packages/form/src/form";
  import ElDivider from "../../node_modules/element-ui/packages/divider/src/main";
  import ElUpload from "../../node_modules/element-ui/packages/upload/src/index";
  export default {
    components: {
      ElUpload,
      ElDivider,
      ElForm,
      ElMenu},
    data() {
      return {
        doUpload:'api/upload',
          user:{
              userId:'',
              username:'',
              email:'',
              password:'',
              pic:''
          },
          repeatpassword:'',
        newpw:'',
          newpic:''
      }
    },
    mounted(){
      this.getUser();
    },
    methods: {
      beforeUpload(file){
        var url='api/upload'
        let fd=new FormData();
        fd.append('file',file);
        axios.post(url,fd).then(res=>{
          if(res!=''){
            this.user.pic=res.data;
          }else{
            this.$message.error('上传失败，请检查您的网络')
          }
        })
      },
      handleSelect(key, keyPath) {
        console.log(key, keyPath);
      },
      //返回主菜单（news页面）
      goback:function () {
        this.$router.push({path:'/news'})
      },
      //获取用户修改前的信息
      getUser:function () {
          var id = this.$parent.user.userId
          var url = 'api/findSession'
          axios.get(url).then(res=>{
              this.user=res.data;
          })
      },
    //修改用户信息方法
      userupdate:function () {
        var url = 'api/saveAndFlushUser'
        axios.post(url,this.user).then(res=>{
            if (res.data=='ok'){
              const h = this.$createElement;
              this.$notify({
                title: '成功',
                message: h('i', { style: 'color: teal'}, '修改成功，已返回')
              });
              this.$router.push({path:'/news'})
            }else{
              this.$alert('修改失败，请稍后重试', '提示', {
                confirmButtonText: '确定',
                callback: action => {
                }
              });
            }
        })
      },
      check:function () {
        var reg = /^[0-9]{6,11}$/;
        if(this.user.password==''||reg.test(this.user.password)==false){
          this.$message.error('密码格式不正确，6-11位数字')
          this.user.password=''
        }
      },
      confirmpassword:function () {
        if(this.user.password!=this.repeatpassword){
          this.$message.error('密码重复输入错误，请重试')
          this.repeatpassword==''
        }else{
            this.user.password==this.repeatpassword
//          alert(this.user.password);
        }
      }
    }
  }
</script>
<style >
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
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

</style>
