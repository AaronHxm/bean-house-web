<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="养老院名称" prop="beadHouseName">
            <el-input v-model="form.beadHouseName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="院长名称" prop="deanName">
            <el-input v-model="form.deanName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="养老院评价" prop="beadHouseEvaluate">
            <el-input v-model="form.beadHouseEvaluate" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="床位数" prop="bedNum">
            <el-input v-model="form.bedNum" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="联系电话" prop="beadPhone">
            <el-input v-model="form.beadPhone" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="养老院地址">
            <el-input v-model="form.beadHouseAddress" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.status.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
        <el-table-column prop="beadHouseId" label="id" />
        <el-table-column prop="beadHouseName" label="养老院名称" />
        <el-table-column prop="deanName" label="院长名称" />
        <el-table-column prop="beadHouseEvaluate" label="养老院评价" />
        <el-table-column prop="bedNum" label="床位数" />
        <el-table-column prop="createBy" label="创建人" />
        <el-table-column prop="createTime" label="创建时间" />
        <el-table-column prop="updateBy" label="修改人" />
        <el-table-column prop="updateTime" label="修改时间" />
        <el-table-column prop="beadPhone" label="联系电话" />
        <el-table-column prop="beadHouseAddress" label="养老院地址" />
        <el-table-column v-if="checkPer(['admin','beadHouseInfo:edit','beadHouseInfo:del'])" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudBeadHouseInfo from '@/api/beadHouseInfo'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { beadHouseId: null, beadHouseName: null, deanId: null, deanName: null, beadHouseEvaluate: null, bedNum: null, createBy: null, createTime: null, updateBy: null, updateTime: null, beadPhone: null, beadHouseAddress: null }
export default {
  name: 'BeadHouseInfo',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '养老院管理', url: 'api/beadHouseInfo', idField: 'beadHouseId', sort: 'beadHouseId,desc', crudMethod: { ...crudBeadHouseInfo }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'beadHouseInfo:add'],
        edit: ['admin', 'beadHouseInfo:edit'],
        del: ['admin', 'beadHouseInfo:del']
      },
      rules: {
        beadHouseName: [
          { required: true, message: '养老院名称不能为空', trigger: 'blur' }
        ],
        deanName: [
          { required: true, message: '院长名称不能为空', trigger: 'blur' }
        ],
        beadHouseEvaluate: [
          { required: true, message: '养老院评价不能为空', trigger: 'blur' }
        ],
        bedNum: [
          { required: true, message: '床位数不能为空', trigger: 'blur' }
        ],
        beadPhone: [
          { required: true, message: '联系电话不能为空', trigger: 'blur' }
        ]
      }    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
