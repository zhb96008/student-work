<template>
    <div class="addTask">
        <div class="item_add">
            <div class="left el-col-10 el-col-offset-1">
                <!--表单-->
                <el-form :model="me" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
                    <!--用户角色-->
                    <el-form-item label="用户角色">
                        <el-input disabled v-model="me.roles.data[0].display_name"></el-input>
                    </el-form-item>
                    <!--所属学院-->
                    <el-form-item v-if="me.college_name === null" label="所属学院">
                        <el-input disabled v-model="me.college_name"></el-input>
                    </el-form-item>
                    <!--用户ID-->

                    <el-form-item label="用户名">
                        <el-input disabled v-model="me.name" placeholder="请输入用户名(推荐使用工号)"></el-input>
                    </el-form-item>
                      <!--用户名-->
                    <el-form-item label="用户昵称" prop="nickname">
                        <el-input v-model="me.nickname" placeholder="请输入用户昵称"></el-input>
                    </el-form-item>
                    
                    <!--性别-->
                    <el-form-item label="性别" prop="gender">
                        <div style="margin-left: 25px;">
                         <el-radio v-for="item in genders" :key=item.id class="radio" v-model="me.gender" :label="item.gender">{{item.gender_str}}</el-radio>
                        </div>
                    </el-form-item>
                    <!--上传头像-->
                    <el-form-item label="上传头像" prop="picture">
                        <el-upload
                         style="margin-left: 25px;"
                                class="upload-demo"
                                action="api/upload"
                                :on-success="handleSuccess"
                        >
                            <el-button size="small" type="primary">{{!me.picture? '点击上传头像':'点击修改头像'}}</el-button>
                        </el-upload>
                        <img v-if="me.picture" style="border-radius:50%;width:50px;margin-top:20px;height:50px;margin-left:-300px;" width="100%" :src="me.picture">
                        <span v-else-if="!me.picture">您还没有上传过头像哦</span>
                    </el-form-item>
                      <!--用户名-->
                    <el-form-item label="手机号码" prop="telephone">
                        <el-input v-model="me.telephone" placeholder="请输入手机号码"></el-input>
                    </el-form-item>
                    <!--邮箱-->
                    <el-form-item label="邮箱" prop="email">
                        <el-input v-model="me.email" type="email" placeholder="请输入邮箱"></el-input>
                    </el-form-item>
                    <!--是否修改密码按钮-->
                    <button class="isPass el-icon-arrow-down" v-if="!isPass" @click="isPass = true">&emsp;修改密码</button>
                    <!--密码-->
                    <el-form-item label="新密码" prop="password" v-if="isPass">
                        <el-input v-model="me.password" type="password" placeholder="请输入新密码"></el-input>
                    </el-form-item>
                    <!--确认密码-->
                    <el-form-item label="确认新密码" prop="password_confirmation" v-if="isPass">
                        <el-input v-model="me.password_confirmation" type="password" placeholder="请输入新的确认密码"></el-input>
                    </el-form-item>

                    <!--按钮组-->
                    <el-form-item class="btnGroup">
                        <el-button type="primary" @click="editUser('ruleForm')">立即修改</el-button>
                        <el-button @click="resetForm('ruleForm')">重置</el-button>
                    </el-form-item>
                </el-form>
            </div>

        </div>
    </div>
</template>
<script>
    import { mapState } from 'vuex'
    export default{
        data () {
            return {
                // 是否显示修改密码
                isPass: false,
                // 是否显示学院选项
                isCollege: false,
                // 是否是修改
                isEdit: false,
                // 学院列表
                collegesList: [],
                // 角色列表
                rolesList: [],
                // 性别
                genders: [
                    {gender_str: '男', gender: false, id: 0},
                    {gender_str: '女', gender: true, id: 1}
                ],
                dialogVisible: false,
                rules: {
                    name: [
                        { type: 'string', required: true, message: '请填写用户名或者工号', trigger: 'change' }
                    ],
                    nickname: [
                     { type: 'string', required: true, message: '请填写用户昵称', trigger: 'change' }
                    ],
                    email: [
                        { type: 'string', required: true, message: '请填写邮箱', trigger: 'blur' }
                    ],
                    college_id: [
                        { type: 'number', required: true, message: '请选择所属学院', trigger: 'change' }
                    ],
                    gender: [
                        {type: 'boolean', required: true, message: '请选择性别', trigger: 'change' }
                    ],
                    password: [
                        { type: 'string', required: true, message: '请填写密码', trigger: 'blur' }
                    ],
                    password_confirmation: [
                        { type: 'string', required: true, message: '请填写确认密码', trigger: 'blur' }
                    ],
                    telephone: [
                        { required: true, message: '请填写手机号码', trigger: 'blur' }
                    ]
                }
            }
        },
        computed: {
            me () {
                return this.$store.state.me ? this.$store.state.me : {};
            }
        },
        methods: {
            // 修改任务
            editUser (formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        this.$http.post('update_user/' + this.me.id, this.$diff.diff(this.me)).then(res => {
                            this.$message({
                                message: '修改用户成功',
                                type: 'success'
                            })
                            this.$router.push({name: 'home'})
                        }).catch(err => {
                            for(let i in err.response.data.errors){
                                this.$message({
                                  type: 'error',
                                  message: err.response.data.errors[i]
                              })  
                            }
                                                         
                          })
                    } else {
                        return false
                    }
                })
            },
            // 重置
            resetForm (formName) {
                this.$refs[formName].resetFields()
            },
            handlePreview(file) {
                this.me.picture = file.url
                this.dialogVisible = true
            },
            handleSuccess(response){
                this.me.picture = response.path
            }
        }
    }
</script>
<style scoped>
    .item_add{
        height:100%;
        background:#fff;
    }
    .left{
        margin-top:30px;
    }
    .el-select{
        float:left;
    }
    .addTask{
        height:100%;
    }
    .radio{
        margin-left:-280px;
    }
    .el-upload>.el-button{
        margin-left:-280px;
    }
    .btnGroup{
        margin-top:20px;
        margin-left:-100px;
    }
    .btnGroup>button{
        margin-right:30px;
    }
    .isPass{
        width:200px;
        cursor:pointer;
        height:30px;
        border:1px solid #1D8CE0;
        background:transparent;
        border-radius:3px;
        color:#1D8CE0;

    }
</style>
