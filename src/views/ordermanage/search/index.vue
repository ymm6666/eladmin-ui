<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
                <label class="el-form-item-label">客户姓名</label>
                <el-input v-model="query.customerName" clearable placeholder="客户姓名" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">客户电话</label>
                <el-input v-model="query.ustomerPhone" clearable placeholder="客户电话" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">客户地址</label>
                <el-input v-model="query.customerAddress" clearable placeholder="客户地址" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">备注</label>
                <el-input v-model="query.remark" clearable placeholder="备注" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">销售负责人</label>
                <el-input v-model="query.saleName" clearable placeholder="销售负责人" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">设计负责人</label>
                <el-input v-model="query.designName" clearable placeholder="设计负责人" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">报价负责人</label>
                <el-input v-model="query.budgetName" clearable placeholder="报价负责人" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">销售单位</label>
                <el-input v-model="query.companyName" clearable placeholder="销售单位" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">单位联系方式</label>
                <el-input v-model="query.companyPhone" clearable placeholder="单位联系方式" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">单位地址</label>
                <el-input v-model="query.companyAddress" clearable placeholder="单位地址" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <date-range-picker
          v-model="query.sumpriceJpy"
          start-placeholder="sumpriceJpyStart"
          end-placeholder="sumpriceJpyStart"
          class="date-item"
        />
        <date-range-picker
          v-model="query.sumpriceRmb"
          start-placeholder="sumpriceRmbStart"
          end-placeholder="sumpriceRmbStart"
          class="date-item"
        />
        <date-range-picker
          v-model="query.createTime"
          start-placeholder="createTimeStart"
          end-placeholder="createTimeStart"
          class="date-item"
        />
        <date-range-picker
          v-model="query.updateTime"
          start-placeholder="updateTimeStart"
          end-placeholder="updateTimeStart"
          class="date-item"
        />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="客户姓名">
            <el-input v-model="form.customerName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="客户电话">
            <el-input v-model="form.ustomerPhone" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="客户地址">
            <el-input v-model="form.customerAddress" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="当日汇率">
            <el-input v-model="form.moneyRate" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单总合计JPY">
            <el-input v-model="form.sumpriceJpy" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单总合计RMB">
            <el-input v-model="form.sumpriceRmb" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="备注">
            <el-input v-model="form.remark" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="销售负责人">
            <el-input v-model="form.saleName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="设计负责人">
            <el-input v-model="form.designName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报价负责人">
            <el-input v-model="form.budgetName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="销售单位">
            <el-input v-model="form.companyName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="单位联系方式">
            <el-input v-model="form.companyPhone" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="单位地址">
            <el-input v-model="form.companyAddress" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="创建时间">
            <el-date-picker v-model="form.createTime" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="更新时间">
            <el-date-picker v-model="form.updateTime" type="datetime" style="width: 370px;" />
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
        <el-table-column prop="customerName" label="客户姓名" />
        <el-table-column prop="ustomerPhone" label="客户电话" />
        <el-table-column prop="customerAddress" label="客户地址" />
        <el-table-column prop="moneyRate" label="当日汇率" />
        <el-table-column prop="sumpriceJpy" label="订单总合计JPY" />
        <el-table-column prop="sumpriceRmb" label="订单总合计RMB" />
        <el-table-column prop="remark" label="备注" />
        <el-table-column prop="saleName" label="销售负责人" />
        <el-table-column prop="designName" label="设计负责人" />
        <el-table-column prop="budgetName" label="报价负责人" />
        <el-table-column prop="companyName" label="销售单位" />
        <el-table-column prop="companyPhone" label="单位联系方式" />
        <el-table-column prop="companyAddress" label="单位地址" />
        <el-table-column prop="createTime" label="创建时间" />
        <el-table-column prop="updateTime" label="更新时间" />
        <el-table-column v-if="checkPer(['admin','orderMain:edit','orderMain:del'])" label="操作" width="150px" align="center">
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
import crudOrderMain from '@/api/orderMain'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { orderMainId: null, customerName: null, ustomerPhone: null, customerAddress: null, moneyRate: null, sumpriceJpy: null, sumpriceRmb: null, remark: null, saleName: null, designName: null, budgetName: null, companyName: null, companyPhone: null, companyAddress: null, createTime: null, updateTime: null }
export default {
  name: 'OrderMain',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '订单管理', url: 'api/orderMain', idField: 'orderMainId', sort: 'orderMainId,desc', crudMethod: { ...crudOrderMain }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'orderMain:add'],
        edit: ['admin', 'orderMain:edit'],
        del: ['admin', 'orderMain:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'customerName', display_name: '客户姓名' },
        { key: 'ustomerPhone', display_name: '客户电话' },
        { key: 'customerAddress', display_name: '客户地址' },
        { key: 'remark', display_name: '备注' },
        { key: 'saleName', display_name: '销售负责人' },
        { key: 'designName', display_name: '设计负责人' },
        { key: 'budgetName', display_name: '报价负责人' },
        { key: 'companyName', display_name: '销售单位' },
        { key: 'companyPhone', display_name: '单位联系方式' },
        { key: 'companyAddress', display_name: '单位地址' }
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
