<template lang="html">
  <div class="">
    <el-row>
      <el-col :span="10" :offset="7">
        <el-tabs v-model="activeName" >
          <el-tab-pane label="用户登录" name="first">
            <el-col >
              <el-form :model="dynamicValidateForm" label-width="100px" ref="dynamicValidateForm">
                    <el-form-item
                      prop="email"
                      label="邮箱"
                      :rules="rules.email"
                    >
                      <el-input v-model="dynamicValidateForm.email"></el-input>
                    </el-form-item>
                    <el-form-item
                    prop="password"
                    label="密码"
                    :rules = "rules.password"
                    >
                      <el-input type="password" v-model="dynamicValidateForm.password"></el-input>
                    </el-form-item>
                    <el-button type="primary" @click="submitForm('dynamicValidateForm')">登录</el-button>
                    <el-button @click="resetForm('dynamicValidateForm')">重置</el-button>
                </el-form>
            </el-col>
          </el-tab-pane>
          <el-tab-pane label="用户注册" name="second">
              <Register></Register>
          </el-tab-pane>
      </el-tabs>
    </el-col>
    </el-row>
  </div>
</template>

<script>
import Register from './Register'
import * as types from '../store/types'
import api from '../axios'
export default{
    name:'login',
    components:{
        Register
    },
    data(){
        return {
            dynamicValidateForm: {
                email:'',
                password:''
            },
            activeName: this.$store.state.activeName,
            rules:  {
                email:[{
                    required :true,
                    message: '请输入邮箱地址',
                    trigger: 'blur'
                },
                {
                    type: 'email',
                    message: '请输入正确的邮箱格式',
                    trigger: 'blur'
                }],
                password: {
                    required :true,
                    message: '请输入密码',
                    trigger: 'blur'
                }
            }
        }
    },
    methods:{
        resetForm(formName) {
            this.$refs[formName].resetFields();
        },
        submitForm(formName){
             this.$refs[formName].validate((valid) =>{
                if (valid) {
                    let opt=this.dynamicValidateForm
                    api.UserLogin(opt).then(
                        ({data})=>{
                            console.log(data)
                            if(data.success){
                                this.$message({
                                    type:'success',
                                    message:'登陆成功'
                                })
                                this.$store.dispatch('UserLogin',data.token)
                                this.$store.dispatch('UserName',data.email)
                                let redirect = decodeURIComponent(this.$route.query.redirect || '/')
                                this.$router.push({
                                    path: redirect
                                })
                            }
                            else {
                                if(data.info==false){
                                    this.$message({
                                        type:'info',
                                        message:'账号不存在'
                                    })
                                }
                                else{
                                    this.$message({
                                        type:'info',
                                        message: '密码不存在'
                                    })
                                }
                            }
                        })
                }
                else{
                    console.log('Error Submit!!');
                    return false;
                }
            })
        }
    }
}
    
</script>

<style >

</style>
