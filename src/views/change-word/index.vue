<template>
<div class="app-container">
    <div style="width:600px;margin:0 auto;">
          <el-form :model="form" ref="passForm">
      <el-form-item label="旧密码" :rules="[
      {
          required:true,
          message:'必填!'
      }]">
          <el-input type="password" v-model="form.oldPassword" placeholder="输入旧密码"></el-input>
      </el-form-item>
      <el-form-item label="新密码" :rules="[
      {
          required:true,
          message:'新密码必填',
          type:'string'
      },{
          min:6,
          max:12,
          type:'string',
          message:'请输入6-12位新密码'
      }]">
          <el-input type="password" v-model="form.newPassword" placeholder="输入新密码"></el-input>
      </el-form-item>
      <el-form-item>
          <el-button type="primary" @click="submit">确认</el-button>
      </el-form-item>
    </el-form>
    </div>
  
</div>
    
</template>
<script>
import {updatePasswordApi} from '@/api/user';
import {removeToken} from '@/utils/auth'
export default {
    data(){
        return {
            form:{
                oldPassword:"",
                newPassword:""
            }
        }
    },
    methods:{
        submit(){
            this.$refs['passForm'].validate().then(() => {
                const {form} = this;
                updatePasswordApi(form).then(() => {
                    this.$message.success('密码更新成功,即将重新登录');
                    removeToken()
                    setTimeout(() => {
                        this.$router.push('/login')
                    }, 1000);
                })
            })
        }
    }
}
</script>