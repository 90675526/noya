<template>
  <div>
    <el-button type="success" size="small" @click.prevent=" toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <el-table :data="customers">
      <el-table-column prop="id" label="编号" />
      <el-table-column prop="gender" label="性别" />
      <el-table-column prop="realname" label="姓名" />
      <el-table-column prop="telephone" label="联系方式" />
      <el-table-column prop="label" label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">{{ slot.vue }}修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- 表格结束-->
    <!--分页-->
    <!-- <el-pagination
      background
      layout="prev, pager, next"
      :total="50"
    /> -->
    <!--模态框-->
    <el-dialog
      title="录入顾客信息"
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
          <el-input v-model="form.password" type="password" />
        </el-form-item>
        <el-form-item label="真实姓名">
          <el-input v-model="form.realname" />
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="form.telephone" />
        </el-form-item>
      </el-form>
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
  // 用于存放网页中要放的数据
  data() {
    return {
      visible: false,
      customers: [],
      form: {
        type: 'customer'
      }
    }
  },
  created() {
    // this为当球vue实例对象
    // vue实例加载完毕
    // const url = 'http://localhost:6677/customer/findAll'
    // request.get(url).then((response) => {
    //   this.customers = response.data
    //   // 将查询结果设置到customers，this指向外面的函数
    // })
    this.loadData()
  },
  // 用于存放网页中要调用的方法
  methods: {
    // json
    // this.form 对象----字符串-->后台{type:'customer',age:12}
    loadData() {
      const url = 'http://localhost:6677/customer/findAll'
      request.get(url).then((response) => {
        this.customers = response.data
      // 将查询结果设置到customers，this指向外面的函数
      })
    },
    submitHandler() {
      const url = 'http://localhost:6677/customer/saveOrUpdate'
      request({
        url,
        method: 'POST',
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
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
    toDeleteHandler(id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 调用后台接口，完成删除操作
        // eslint-disable-next-line no-unused-vars
        const url = 'http://localhost:6677/customer/deleteById?id=' + id
        request.get(url).then((response) => {
        // 1.刷新数据
          this.loadData()
          // 2. 提示结果
          this.$message({
            type: 'success',
            message: 'response.message'
          })
        })
      // 确认
      })
    },
    toUpdateHandler(row) {
      this.title = '修改顾客信息'
      this.form = row
      this.visible = true
    },
    toAddHandler() {
      this.form = {
        type: 'customer'
      }
      this.visible = true
    },
    closeModalHandler() {
      this.visible = false
    }

  }
}// 暴漏接口，让其他模块接受
</script>
<style scoped>
</style>

