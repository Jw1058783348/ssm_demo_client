<template>
  <div id="app">
    <el-table
    :data="userList"
    style="width: 100%"
    @selection-change="handleSelectionChange">
    <el-table-column
      type="selection"
      prop="id"
      width="55">
    </el-table-column>
    <el-table-column
      label="姓名"
      prop="username"
      width="180">
    </el-table-column>
    <el-table-column
      label="密码"
      prop="password"
      width="180">
    </el-table-column>
    <el-table-column label="操作">
      <template slot="header">
        <el-button type="primary" size="mini" @click="dialogTableVisible = true">添加</el-button>
        <el-button type="danger" size="mini"  @click="handleBatchDelete">批量删除</el-button>
      </template>
      <template slot-scope="scope">
        <el-button
          size="mini"
          @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
        <el-button
          size="mini"
          type="danger"
          @click="handleDelete(scope.$index, scope.row)">删除</el-button>
      </template>
    </el-table-column>
  </el-table>
  <el-dialog  :visible.sync="dialogTableVisible">
    <userform :user=row @closeDialog='dialogCallback'></userform>
</el-dialog>
  </div>
</template>

<script>
import userform from './components/form.vue'
export default {
  name: 'App',
  components: {
    userform
  },
  data () {
    return {
      userList: [],
      row: {
        id: 0,
        username: '',
        password: ''
      },
      multipleSelection: [],
      dialogTableVisible: false
    }
  },
  watch: {
    dialogTableVisible (val) {
      if (!val) {
        Object.assign(this.row, {
          id: 0,
          username: '',
          password: ''
        })
      }
    }
  },
  created () {
    this.getUserList()
  },
  methods: {
    handleSelectionChange (val) {
      this.multipleSelection = val.map(v => v.id)
      console.log(this.multipleSelection)
    },
    handleEdit (index, row) {
      Object.assign(this.row, row)
      this.dialogTableVisible = true
    },
    async handleDelete (index, row) {
      let res = await this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
      if (res === 'confirm') {
        let resp = await Window.axios.delete('/api/users/' + row.id)
        if (resp.data.status === 1) {
          this.$message.success(resp.data.message)
          this.getUserList()
        } else {
          this.$message.error(resp.data.message)
        }
      }
    },
    async handleBatchDelete () {
      if (this.multipleSelection.length === 0) {
        this.$message.warn('请选中要删除的用户！')
        return
      }
      let res = await this.$confirm('此操作将永久删除这些用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
      if (res === 'confirm') {
        let resp = await Window.axios.delete('/api/users/' + this.multipleSelection)
        if (resp.data.status === 1) {
          this.$message.success(resp.data.message)
          this.getUserList()
        } else {
          this.$message.error(resp.data.message)
        }
      }
    },
    async getUserList () {
      let res = await this.$axios.get('/api/users')
      if (res.data.status === 1) {
        this.userList = res.data.data
      }
    },
    dialogCallback (e) {
      this.dialogTableVisible = e
      this.getUserList()
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
