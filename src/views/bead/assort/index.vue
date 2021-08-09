<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission"/>
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU"
                 :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <!--          <el-form-item label="创建人">-->
          <!--            <el-input v-model="form.createBy" style="width: 370px;" />-->
          <!--          </el-form-item>-->
          <!--          <el-form-item label="创建时间">-->
          <!--            <el-input v-model="form.createTime" style="width: 370px;" />-->
          <!--          </el-form-item>-->
          <!--          <el-form-item label="修改人">-->
          <!--            <el-input v-model="form.updateBy" style="width: 370px;" />-->
          <!--          </el-form-item>-->
          <!--          <el-form-item label="修改时间">-->
          <!--            <el-input v-model="form.updateTime" style="width: 370px;" />-->
          <!--          </el-form-item>-->
          <el-form-item label="描述">
            <el-input v-model="form.remarks" style="width: 370px;"/>
          </el-form-item>
          <el-form-item label="设施名称">
            <el-input v-model="form.assortName" style="width: 370px;"/>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.status.cu === 2" type="primary" @click="crud.submitCU">确认
          </el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small"
                style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55"/>
        <el-table-column prop="createBy" label="创建人"/>
        <el-table-column prop="createTime" label="创建时间"/>
        <el-table-column prop="updateBy" label="修改人"/>
        <el-table-column prop="updateTime" label="修改时间"/>
        <el-table-column prop="remarks" label="描述"/>
        <el-table-column prop="assortName" label="设施名称"/>
        <el-table-column v-if="checkPer(['admin','bedAssort:edit','bedAssort:del'])" label="操作"
                         width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination/>
    </div>
  </div>
</template>

<script>
  import crudBedAssort from '@/api/bedAssort'
  import CRUD, {presenter, header, form, crud} from '@crud/crud'
  import rrOperation from '@crud/RR.operation'
  import crudOperation from '@crud/CRUD.operation'
  import udOperation from '@crud/UD.operation'
  import pagination from '@crud/Pagination'

  const defaultForm = {
    id: null,
    createBy: null,
    createTime: null,
    updateBy: null,
    updateTime: null,
    tenantId: null,
    status: null,
    remarks: null,
    assortName: null
  }
  export default {
    name: 'BedAssort',
    components: {pagination, crudOperation, rrOperation, udOperation},
    mixins: [presenter(), header(), form(defaultForm), crud()],
    cruds() {
      return CRUD({
        title: '床位配套',
        url: 'api/bedAssort',
        idField: 'id',
        sort: 'id,desc',
        crudMethod: {...crudBedAssort}
      })
    },
    data() {
      return {
        permission: {
          add: ['admin', 'bedAssort:add'],
          edit: ['admin', 'bedAssort:edit'],
          del: ['admin', 'bedAssort:del']
        },
        rules: {}
      }
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
