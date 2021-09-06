<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">姓名</label>
        <el-input v-model="query.userRealName" clearable placeholder="姓名" style="width: 185px;"
                  class="filter-item" @keyup.enter.native="crud.toQuery"/>
        <label class="el-form-item-label">手机号</label>
        <el-input v-model="query.userPhone" clearable placeholder="手机号" style="width: 185px;"
                  class="filter-item" @keyup.enter.native="crud.toQuery"/>
        <label class="el-form-item-label">身份证号码</label>
        <el-input v-model="query.userIdCard" clearable placeholder="身份证号码" style="width: 185px;"
                  class="filter-item" @keyup.enter.native="crud.toQuery"/>
        <label class="el-form-item-label">是否是会员</label>
        <el-input v-model="query.isVip" clearable placeholder="是否是会员" style="width: 185px;"
                  class="filter-item" @keyup.enter.native="crud.toQuery"/>
        <label class="el-form-item-label">会员等级</label>
        <el-input v-model="query.vipLevel" clearable placeholder="会员等级" style="width: 185px;"
                  class="filter-item" @keyup.enter.native="crud.toQuery"/>
        <rrOperation :crud="crud"/>
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission"/>
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU"
                 :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="姓名" prop="userRealName">
            <el-input v-model="form.userRealName" style="width: 370px;"/>
          </el-form-item>
          <el-form-item label="昵称" prop="userName">
            <el-input v-model="form.userName" style="width: 370px;"/>
          </el-form-item>
          <el-form-item label="手机号" prop="userPhone">
            <el-input v-model="form.userPhone" style="width: 370px;"/>
          </el-form-item>
          <el-form-item label="身份证号码" prop="userIdCard"   label-width="auto"
          >
            <el-input v-model="form.userIdCard" style="width: 350px;"/>
          </el-form-item>
          <el-form-item label="密码" prop="userPwd">
            <el-input v-model="form.userPwd" style="width: 370px;"/>
          </el-form-item>
          <el-form-item label="省">
            <el-cascader
              size="large"
              :options="options"
              v-model="selectedOptions"
              @change="handleChange" style="width: 370px;">
            </el-cascader>
          </el-form-item>
          <el-form-item label="地址" prop="userAddress">
            <el-input v-model="form.userAddress" style="width: 370px;"/>
          </el-form-item>
          <el-form-item label="是否是会员" prop="isVip"   label-width="auto"
          >
            <el-select v-model="form.isVip" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.is_vip"
                :key="item.id"
                :label="item.label"
                :value="item.value"/>
            </el-select>
          </el-form-item>
          <el-form-item label="邀请码">
            <el-input v-model="form.inviteCode" style="width: 370px;"/>
          </el-form-item>
          <el-form-item label="邀请人">
            <el-input v-model="form.inviteUser" style="width: 370px;"/>
          </el-form-item>
          <el-form-item label="会员等级" prop="vipLevel">
            <el-select v-model="form.vipLevel" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.vip_level"
                :key="item.id"
                :label="item.label"
                :value="item.value"/>
            </el-select>
          </el-form-item>
          <el-form-item label="会员积分" prop="vipIntegral">
            <el-input v-model="form.vipIntegral" style="width: 370px;"/>
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
        <el-table-column prop="userRealName" label="姓名"/>
        <el-table-column prop="userName" label="昵称"/>
        <el-table-column prop="userPhone" label="手机号"/>
        <el-table-column prop="userIdCard" label="身份证号码"/>
        <el-table-column prop="userPwd" label="密码"/>
        <el-table-column prop="userProvince" label="省"/>
        <el-table-column prop="userCity" label="市"/>
        <el-table-column prop="userArea" label="区"/>
        <el-table-column prop="userAddress" label="地址"/>
        <el-table-column prop="isVip" label="是否是会员">
          <template slot-scope="scope">
            {{ dict.label.is_vip[scope.row.isVip] }}
          </template>
        </el-table-column>
        <el-table-column prop="inviteCode" label="邀请码"/>
        <el-table-column prop="inviteUser" label="邀请人"/>
        <el-table-column prop="vipLevel" label="会员等级">
          <template slot-scope="scope">
            {{ dict.label.vip_level[scope.row.vipLevel] }}
          </template>
        </el-table-column>
        <el-table-column prop="vipIntegral" label="会员积分"/>
        <el-table-column prop="createTime" label="创建时间"/>
        <el-table-column v-if="checkPer(['admin','account:edit','account:del'])" label="操作"
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
  import crudAccount from '@/api/account'
  import CRUD, {presenter, header, form, crud} from '@crud/crud'
  import rrOperation from '@crud/RR.operation'
  import crudOperation from '@crud/CRUD.operation'
  import udOperation from '@crud/UD.operation'
  import pagination from '@crud/Pagination'
  import {regionData, CodeToText} from "element-china-area-data";

  const defaultForm = {
    id: null,
    userRealName: null,
    userName: null,
    userPhone: null,
    userIdCard: null,
    userPwd: null,
    userProvince: null,
    userCity: null,
    userArea: null,
    userAddress: null,
    isVip: null,
    inviteCode: null,
    inviteUser: null,
    vipLevel: null,
    vipIntegral: null,
    createBy: null,
    createTime: null,
    updateBy: null,
    updateTime: null,
    tenantId: null,
    status: null,
    remarks: null
  }
  export default {
    name: 'Account',
    components: {pagination, crudOperation, rrOperation, udOperation},
    mixins: [presenter(), header(), form(defaultForm), crud()],
    dicts: ['is_vip', 'vip_level'],
    cruds() {
      return CRUD({
        title: 'account/',
        url: 'api/account',
        idField: 'id',
        sort: 'id,desc',
        crudMethod: {...crudAccount}
      })
    },
    data() {
      return {
        options: regionData,
        selectedOptions: [],
        permission: {
          add: ['admin', 'account:add'],
          edit: ['admin', 'account:edit'],
          del: ['admin', 'account:del']
        },
        rules: {
          userRealName: [
            {required: true, message: '姓名不能为空', trigger: 'blur'}
          ],
          userName: [
            {required: true, message: '昵称不能为空', trigger: 'blur'}
          ],
          userPhone: [
            {required: true, message: '手机号不能为空', trigger: 'blur'}
          ],
          userIdCard: [
            {required: true, message: '身份证号码不能为空', trigger: 'blur'}
          ],
          userPwd: [
            {required: true, message: '密码不能为空', trigger: 'blur'}
          ],
          userAddress: [
            {required: true, message: '地址不能为空', trigger: 'blur'}
          ],
          isVip: [
            {required: true, message: '是否是会员不能为空', trigger: 'blur'}
          ],
          vipLevel: [
            {required: true, message: '会员等级不能为空', trigger: 'blur'}
          ],
          vipIntegral: [
            {required: true, message: '会员积分不能为空', trigger: 'blur'}
          ]
        },
        queryTypeOptions: [
          {key: 'userRealName', display_name: '姓名'},
          {key: 'userPhone', display_name: '手机号'},
          {key: 'userIdCard', display_name: '身份证号码'},
          {key: 'isVip', display_name: '是否是会员'},
          {key: 'vipLevel', display_name: '会员等级'}
        ]
      }
    },
    methods: {
      handleChange(value) {


        this.form.userProvince = CodeToText[this.selectedOptions[0]]
        this.form.userCity = CodeToText[this.selectedOptions[1]]
        this.form.userArea = CodeToText[this.selectedOptions[2]]


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
