<template>
  <div>
    <el-button type="success" size="small" @click.prevent="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <!----表格--->
    <el-table :data="products">
      <el-table-column fixed="left" prop="id" label="编号" />
      <el-table-column fixed="left" prop="name" label="产品名称" />
      <el-table-column prop="price" label="价格" />
      <el-table-column prop="description" label="描述" />
      <el-table-column prop="status" label="所属产品" />
      <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler">修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!----/模态框--->
    <el-pagination
      background
      layout="prev, pager, next"
      :total="50"
    />
    <!--模态框-->
    <el-dialog
      title="录入产品信息"
      :visible.sync="visible"
      width="60%"
    >
      <el-form
        :model="form"
        label-width="80px"
      >
        --{{ form }}
        <el-form-item label="编号">
          <el-input v-model="form.id" />
        </el-form-item>
        <el-form-item label="产品名称">
          <el-input v-model="form.name" />
        </el-form-item>
        <el-form-item label="价格">
          <el-input
            v-model="form.price"
          />
        </el-form-item>
        <el-form-item label="描述">
          <el-input v-model="form.description" />
        </el-form-item>
        <el-form-item label="所属产品">
          <el-input v-model="form.status" />
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
      products: [],
      form: {
        type: 'product'
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
      const url = 'http://localhost:6677/product/saveOrUpdate'
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
      const url = 'http://localhost:6677/product/findAll'
      request.get(url).then((response) => {
        // 若不是箭头函数，this意味外部环境中的this，（窗口）
        this.products = response.data
      })
    },
    toUpdateHandler() {
      this.title = '修改产品信息'
      this.visiable = true
    },

    toDeleteHandler(id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      })
    },
    toAddHandler() {
      this.title = '录入产品信息'
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
