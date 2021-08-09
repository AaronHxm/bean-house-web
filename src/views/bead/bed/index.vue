<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="描述">
            <el-input v-model="form.remarks" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="床位编号">
            <el-input v-model="form.bedNum" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="所在楼层">
            <el-input v-model="form.floor" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="所在房间号">
            <el-input v-model="form.room" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="所属养老院">
            <el-select
              v-model="beadHouse"
              style="width: 437px"
              placeholder="请选择"
              @change="change"
            >
              <el-option
                v-for="item in beadHouseInfos"
                :key="item.beanName"
                :label="item.beanName"
                :value="item.id"
              />
            </el-select>
          </el-form-item>
          <el-form-item label="床位价钱">
            <el-input v-model="form.bedPrice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="床位折扣价">
            <el-input v-model="form.bedDiscountPrice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="床位折扣">
            <el-input v-model="form.bedDiscount" style="width: 370px;" />
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
        <el-table-column prop="createBy" label="创建人" />
        <el-table-column prop="createTime" label="创建时间" />
        <el-table-column prop="updateBy" label="修改人" />
        <el-table-column prop="updateTime" label="修改时间" />
        <el-table-column prop="remarks" label="描述" />
        <el-table-column prop="bedNum" label="床位编号" />
        <el-table-column prop="floor" label="所在楼层" />
        <el-table-column prop="room" label="所在房间号" />
        <el-table-column prop="beadHouseInfo.beanName" label="所属养老院" />
        <el-table-column prop="bedPrice" label="床位价钱" />
        <el-table-column prop="bedDiscountPrice" label="床位折扣价" />
        <el-table-column prop="bedDiscount" label="床位折扣" />
        <el-table-column v-if="checkPer(['admin','bedInfo:edit','bedInfo:del'])" label="操作" width="150px" align="center">
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
import crudBedInfo from './bedInfo'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import { getHouseInfo } from '@/api/beadHouseInfo'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
let  modelBean = {}
const defaultForm = {beadHouseInfo: { id: null },id: null, createBy: null, createTime: null, updateBy: null, updateTime: null, tenantId: null, status: null, remarks: null, bedNum: null, floor: null, room: null,  bedPrice: null, bedDiscountPrice: null, bedDiscount: null }
export default {
  name: 'BedInfo',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '床位管理', url: 'api/bedInfo', idField: 'id', sort: 'id,desc', crudMethod: { ...crudBedInfo }})
  },
  data() {
    return {
      beadHouseInfos:[],

      beadHouse:[],
      permission: {
        add: ['admin', 'bedInfo:add'],
        edit: ['admin', 'bedInfo:edit'],
        del: ['admin', 'bedInfo:del']
      },
      rules: {
      }    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    },
    // 新增与编辑前做的操作
    [CRUD.HOOK.afterToCU](crud, form) {
      this.getHouseInfos()
    },
    [CRUD.HOOK.afterValidateCU](crud) {
      crud.form.beadHouseInfo =  modelBean
      return true
    },

    getHouseInfos() {
      getHouseInfo().then(res => {
         console.log('house',res)
         this.beadHouseInfos = res.content
       }).catch(() => { })
    },
    change(value){
       console.log("crud.form.beadHouseInfo,",form)
      console.log("value,",value)

      modelBean = {id:value}
    }
  }
}
</script>

<style scoped>

</style>
