<template>
  <el-form :model="user" status-icon :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
    <h4>{{user.id === 0 ? '添加用户' : '编辑用户'}}</h4>
  <el-form-item label="用户名" prop="username">
    <el-input type="username" v-model="user.username" autocomplete="off"></el-input>
  </el-form-item>
  <el-form-item label="密码" prop="password">
    <el-input type="password" v-model="user.password" autocomplete="off"></el-input>
  </el-form-item>
  <el-form-item>
    <el-button type="primary" @click="submitForm('ruleForm')">提交</el-button>
    <el-button @click="resetForm('ruleForm')">重置</el-button>
  </el-form-item>
</el-form>
</template>
<script>
export default {
  props: {
    user: {
      id: {
        type: Number,
        default: 0
      },
      username: {
        type: String
      },
      password: {
        type: String
      }
    }
  },
  created () {
    console.log(this.user.id)
  },
  data () {
    return {
      rules: {
        username: [
          {required: true, message: '请输入用户名', trigger: 'blur'}
        ],
        password: [
          {required: true, message: '请输入密码', trigger: 'blur'}
        ]
      }
    }
  },
  methods: {
    async submitForm (formName) {
      if (this.user.id === 0) {
        console.log(this.user)
        let res = await this.$axios.post('/api/users', this.user)
        if (res.data.status === 1) {
          this.$message.success(res.data.message)
          this.$emit('closeDialog', false)
        } else {
          this.$message.error(res.data.message)
        }
      } else {
        let res = await this.$axios.put('/api/users/' + this.user.id, this.user)
        if (res.data.status === 1) {
          this.$message.success(res.data.message)
          this.$emit('closeDialog', false)
        } else {
          this.$message.error(res.data.message)
        }
      }
    },
    resetForm (formName) {
      this.$refs[formName].resetFields()
    }
  }
}
</script>
