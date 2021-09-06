<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="院长名称" prop="deanName">
            <el-input v-model="form.deanName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="床位数" prop="bedNum">
            <el-input v-model="form.bedNum" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="名称">
            <el-input v-model="form.beanName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="描述">
            <el-input v-model="form.remarks" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="接待时间">
            <el-input v-model="form.beanTime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="联系电话">
            <el-input v-model="form.beanTel" style="width: 370px;" />
          </el-form-item>

          <el-form-item label="所在地区">
            <el-cascader
              size="large"
              :options="options"
              v-model="selectedOptions"
              @change="handleChange" style="width: 370px;">
            </el-cascader>
          </el-form-item>
          <el-form-item label="位置">
            <el-input v-model="form.beanAddress" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="医护数量">
            <el-input v-model="form.nurseNum" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="描述">
            <el-input v-model="form.beanDesc" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="logo">
            <el-input v-model="form.beanLogo" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="评价">
            <el-input v-model="form.beanHouseEvaluate" style="width: 370px;" />
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
        <el-table-column prop="deanName" label="院长名称" />
        <el-table-column prop="bedNum" label="床位数" />
        <el-table-column prop="createBy" label="创建人" />
        <el-table-column prop="createTime" label="创建时间" />
        <el-table-column prop="updateBy" label="修改人" />
        <el-table-column prop="updateTime" label="修改时间" />
        <el-table-column prop="beanName" label="养老院名称" />
        <el-table-column prop="remarks" label="描述" />
        <el-table-column prop="beanTime" label="养老院接待时间" />
        <el-table-column prop="beanTel" label="养老院电话" />
        <el-table-column prop="beanAddress" label="养老院位置" />
        <el-table-column prop="beanProvince" label="养老院省" />
        <el-table-column prop="beanCity" label="养老院市" />
        <el-table-column prop="beanArea" label="养老院区" />
        <el-table-column prop="nurseNum" label="养老院医护数量" />
        <el-table-column prop="beanDesc" label="养老院描述" />
        <el-table-column prop="beanLogo" label="养老院logo" />
        <el-table-column prop="beanHouseEvaluate" label="评价" />
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
import {regionData,CodeToText} from "element-china-area-data";

const defaultForm = { deanId: null, deanName: null, bedNum: null, createBy: null, createTime: null, updateBy: null, updateTime: null, id: null, beanName: null, tenantId: null, status: null, remarks: null, beanTime: null, beanTel: null, beanAddress: null, lng: null, lat: null, beanProvince: null, beanCity: null, beanArea: null, nurseNum: null, beanDesc: null, beanLogo: null, beanHouseEvaluate: null }
export default {
  name: 'BeadHouseInfo',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '养老院管理', url: 'api/beadHouseInfo', idField: 'id', sort: 'id,desc', crudMethod: { ...crudBeadHouseInfo }})
  },
  data() {
    return {
      options: regionData,
      selectedOptions: [],
      permission: {
        add: ['admin', 'beadHouseInfo:add'],
        edit: ['admin', 'beadHouseInfo:edit'],
        del: ['admin', 'beadHouseInfo:del']
      },
      rules: {
        deanName: [
          { required: true, message: '院长名称不能为空', trigger: 'blur' }
        ],
        bedNum: [
          { required: true, message: '床位数不能为空', trigger: 'blur' }
        ]
      }    }
  },
  methods: {
    handleChange(value) {

      this.form.beanProvince = CodeToText[this.selectedOptions[0]]
      this.form.beanCity = CodeToText[this.selectedOptions[1]]
      this.form.beanArea = CodeToText[this.selectedOptions[2]]


    },
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
