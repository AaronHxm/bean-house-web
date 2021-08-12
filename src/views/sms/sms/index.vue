<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">发件人信息</label>
        <el-input v-model="query.fromUser" clearable placeholder="发件人信息" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">收件人信息</label>
        <el-input v-model="query.toUser" clearable placeholder="收件人信息" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">短信类型</label>
        <el-input v-model="query.smsType" clearable placeholder="短信类型" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="短信内容">
            <el-input v-model="form.smsContent" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="发件人信息">
            <el-input v-model="form.fromUser" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="收件人信息">
            <el-input v-model="form.toUser" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="短信类型">
            <el-select v-model="form.smsType" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.code_status"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="短信code（验证码信息）">
            <el-input v-model="form.smsCode" style="width: 370px;" />
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
        <el-table-column prop="smsContent" label="短信内容" />
        <el-table-column prop="fromUser" label="发件人信息" />
        <el-table-column prop="toUser" label="收件人信息" />
        <el-table-column prop="smsType" label="短信类型">
          <template slot-scope="scope">
            {{ dict.label.code_status[scope.row.smsType] }}
          </template>
        </el-table-column>
        <el-table-column prop="smsCode" label="短信code（验证码信息）" />
        <el-table-column prop="createBy" label="创建人" />
        <el-table-column prop="createTime" label="创建时间" />
        <el-table-column prop="updateBy" label="修改人" />
        <el-table-column prop="updateTime" label="修改时间" />
        <el-table-column prop="remarks" label="描述" />
        <el-table-column v-if="checkPer(['admin','smsInfo:edit','smsInfo:del'])" label="操作" width="150px" align="center">
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
import crudSmsInfo from '@/api/smsInfo'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, smsContent: null, fromUser: null, toUser: null, smsType: null, smsCode: null, createBy: null, createTime: null, updateBy: null, updateTime: null, tenantId: null, status: null, remarks: null }
export default {
  name: 'SmsInfo',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['code_status'],
  cruds() {
    return CRUD({ title: 'sms', url: 'api/smsInfo', idField: 'id', sort: 'id,desc', crudMethod: { ...crudSmsInfo }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'smsInfo:add'],
        edit: ['admin', 'smsInfo:edit'],
        del: ['admin', 'smsInfo:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'fromUser', display_name: '发件人信息' },
        { key: 'toUser', display_name: '收件人信息' },
        { key: 'smsType', display_name: '短信类型' }
      ]
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
