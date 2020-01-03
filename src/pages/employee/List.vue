<template>
  <div>
    <el-button type="success" size="small" @click.prevent="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <!----表格--->
    <el-table :data="employees">
      <el-table-column fixed="left" prop="id" label="编号" />
      <el-table-column fixed="left" prop="username" label="用户名" />
      <el-table-column prop="realname" label="姓名" />
      <el-table-column prop="gender" label="性别" />
      <el-table-column prop="telephone" label="联系方式" />
      <el-table-column width="200" prop="idCard" label="身份证号" />
      <el-table-column width="200" prop="bankCard" label="银行卡号" />
      <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">{{ slot.vue }}修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!----/模态框--->
    <!-- <el-pagination
      background
      layout="prev, pager, next"
      :total="50"
    /> -->
    <!--模态框-->
    <el-dialog
      title="录入员工信息"
      :visible.sync="visible"
      width="60%"
    >
      <el-form
        :model="form"
        label-width="80px"
      >
        --{{ form }}
        <el-form-item label="用户名">
          <el-input v-model="form.username" />
        </el-form-item>
        <el-form-item label="密码">
          <el-input
            v-model="form.password"
            type="password"
          />
        </el-form-item>
        <el-form-item label="姓名">
          <el-input v-model="form.realname" />
        </el-form-item>
        <el-form-item label="性别">
          <el-radio-group v-model="radio">
            <el-radio label="男">男</el-radio>
            <el-radio label="女">女</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="form.telephone" />
        </el-form-item>
        <el-form-item label="身份证号">
          <el-input v-model="form.idCard" />
        </el-form-item>
      </el-form>
      <!-- /模态框 -->
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
  </div></template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  data() {
    return {
      visible: false,
      employees: [],
      radio: '男',
      form: {
        type: 'employee'
      }
    }
  },
  created() {
    // 在页面加载出来加载数据
    this.loadData()
  },
  methods: {
    // 提交
    submitHandler() {
      const url = 'http://localhost:6677/waiter/saveOrUpdate'
      request({
        url,
        // 告诉后台中我的请求体中查的是查询字符串
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
        // 请求集中的数据，将this.form转换为查询字符穿发送给后台
        data: querystring.stringify(this.form)
      }).then((response) => {
        // 模态框关闭
        this.closeModalHandler()
        this.loadData()
        // 刷新
        this.$message({
          type: 'success',
          message: response.message
        })
      })
    },
    // 重载员工数据
    loadData() {
      const url = 'http://localhost:6677/waiter/findAll'
      request.get(url).then((response) => {
        // 若不是箭头函数，this意味外部环境中的this，（窗口）
        this.employees = response.data
      })
    },
    toUpdateHandler(row) {
      this.form = row
      this.title = '修改员工信息'
      this.visiable = true
    },

    toDeleteHandler(id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        const url = 'http://localhost:6677/waiter/deleteById?id=' + id
        request.get(url).then((response) => {
          this.loadData()
          this.$message({
            type: 'success',
            message: '操作成功'
          })
        })
      })
    },
    toAddHandler() {
      this.form = {
        type: 'employee'
      }
      this.title = '录入员工信息'
      this.visible = true
    },
    closeModalHandler() {
      this.visible = false
    }
  }
} // 暴漏接口，让其他模块接受
</script>

<style scoped>

</style>
