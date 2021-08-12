<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">订单号</label>
        <el-input v-model="query.orderId" clearable placeholder="订单号" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">支付金额</label>
        <el-input v-model="query.orderPrice" clearable placeholder="支付金额" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">购买人姓名</label>
        <el-input v-model="query.payUserName" clearable placeholder="购买人姓名" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">购买人联系方式</label>
        <el-input v-model="query.payPhone" clearable placeholder="购买人联系方式" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="订单号">
            <el-input v-model="form.orderId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单类型">
            <el-select v-model="form.orderType" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.order_type"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="支付方式">
            <el-select v-model="form.orderPayType" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.pay_type"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="支付金额">
            <el-input v-model="form.orderPrice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="支付状态">
            <el-select v-model="form.orderStatus" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.pay_status"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="购买人姓名">
            <el-input v-model="form.payUserName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="购买人联系方式">
            <el-input v-model="form.payPhone" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="描述">
            <el-input v-model="form.remarks" :rows="3" type="textarea" style="width: 370px;" />
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
        <el-table-column prop="orderId" label="订单号" />
        <el-table-column prop="orderType" label="订单类型">
          <template slot-scope="scope">
            {{ dict.label.order_type[scope.row.orderType] }}
          </template>
        </el-table-column>
        <el-table-column prop="orderPayType" label="支付方式">
          <template slot-scope="scope">
            {{ dict.label.pay_type[scope.row.orderPayType] }}
          </template>
        </el-table-column>
        <el-table-column prop="orderPrice" label="支付金额" />
        <el-table-column prop="orderStatus" label="支付状态">
          <template slot-scope="scope">
            {{ dict.label.pay_status[scope.row.orderStatus] }}
          </template>
        </el-table-column>
        <el-table-column prop="payUserName" label="购买人姓名" />
        <el-table-column prop="payPhone" label="购买人联系方式" />
        <el-table-column prop="createBy" label="创建人" />
        <el-table-column prop="createTime" label="创建时间" />
        <el-table-column prop="updateBy" label="修改人" />
        <el-table-column prop="updateTime" label="修改时间" />
        <el-table-column prop="remarks" label="描述" />
        <el-table-column v-if="checkPer(['admin','tbOrderInfo:edit','tbOrderInfo:del'])" label="操作" width="150px" align="center">
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
import crudTbOrderInfo from '@/api/tbOrderInfo'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { id: null, orderId: null, orderType: null, orderPayType: null, orderPrice: null, orderStatus: null, payUserId: null, payUserName: null, payPhone: null, createBy: null, createTime: null, updateBy: null, updateTime: null, tenantId: null, status: null, remarks: null }
export default {
  name: 'TbOrderInfo',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['order_type', 'pay_type', 'pay_status'],
  cruds() {
    return CRUD({ title: 'order', url: 'api/tbOrderInfo', idField: 'id', sort: 'id,desc', crudMethod: { ...crudTbOrderInfo }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'tbOrderInfo:add'],
        edit: ['admin', 'tbOrderInfo:edit'],
        del: ['admin', 'tbOrderInfo:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'orderId', display_name: '订单号' },
        { key: 'orderPrice', display_name: '支付金额' },
        { key: 'payUserName', display_name: '购买人姓名' },
        { key: 'payPhone', display_name: '购买人联系方式' }
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
