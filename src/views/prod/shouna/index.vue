<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
                <label class="el-form-item-label">类型</label>
                <el-input v-model="query.shounaName" clearable placeholder="类型" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">项</label>
                <el-input v-model="query.shounaItem" clearable placeholder="项" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">宽深高</label>
                <el-input v-model="query.shounaWidthdeephight" clearable placeholder="宽深高" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">功能</label>
                <el-input v-model="query.shounaFunc" clearable placeholder="功能" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">门板</label>
                <el-input v-model="query.shounaDoor" clearable placeholder="门板" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">所属序列</label>
                <el-input v-model="query.serialFlag" clearable placeholder="所属序列" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">预留1</label>
                <el-input v-model="query.shounaRemark1" clearable placeholder="预留1" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">预留2</label>
                <el-input v-model="query.shounaRemark2" clearable placeholder="预留2" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <date-range-picker
          v-model="query.shounaBaseprice"
          start-placeholder="shounaBasepriceStart"
          end-placeholder="shounaBasepriceStart"
          class="date-item"
        />
        <date-range-picker
          v-model="query.shounaFuncprice"
          start-placeholder="shounaFuncpriceStart"
          end-placeholder="shounaFuncpriceStart"
          class="date-item"
        />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="类型">
            <el-input v-model="form.shounaName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="项">
            <el-input v-model="form.shounaItem" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="宽深高">
            <el-input v-model="form.shounaWidthdeephight" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="功能">
            <el-input v-model="form.shounaFunc" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="门板">
            <el-input v-model="form.shounaDoor" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="标准价格(JPY)">
            <el-input v-model="form.shounaBaseprice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="功能价格(JPY)">
            <el-input v-model="form.shounaFuncprice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="所属序列" prop="serialFlag">
            <el-input v-model="form.serialFlag" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="预留1">
            <el-input v-model="form.shounaRemark1" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="预留2">
            <el-input v-model="form.shounaRemark2" style="width: 370px;" />
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
        <el-table-column prop="shounaName" label="类型" />
        <el-table-column prop="shounaItem" label="项" />
        <el-table-column prop="shounaWidthdeephight" label="宽深高" />
        <el-table-column prop="shounaFunc" label="功能" />
        <el-table-column prop="shounaDoor" label="门板">
          <template slot-scope="scope">
            {{ dict.label.cd_class[scope.row.shounaDoor] }}
          </template>
        </el-table-column>
        <el-table-column prop="shounaBaseprice" label="标准价格(JPY)" />
        <el-table-column prop="shounaFuncprice" label="功能价格(JPY)" />
        <el-table-column prop="serialFlag" label="所属序列">
          <template slot-scope="scope">
            {{ dict.label.cd_serial[scope.row.serialFlag] }}
          </template>
        </el-table-column>
        <el-table-column prop="shounaRemark1" label="预留1" />
        <el-table-column prop="shounaRemark2" label="预留2" />
        <el-table-column v-if="checkPer(['admin','prodShouna:edit','prodShouna:del'])" label="操作" width="150px" align="center">
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
import crudProdShouna from '@/api/prodShouna'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { shounaId: null, shounaName: null, shounaItem: null, shounaWidthdeephight: null, shounaFunc: null, shounaDoor: null, shounaBaseprice: null, shounaFuncprice: null, serialFlag: null, shounaRemark1: null, shounaRemark2: null }
export default {
  name: 'ProdShouna',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['cd_class', 'cd_serial'],
  cruds() {
    return CRUD({ title: '收纳柜维护', url: 'api/prodShouna', idField: 'shounaId', sort: 'shounaId,desc', crudMethod: { ...crudProdShouna }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'prodShouna:add'],
        edit: ['admin', 'prodShouna:edit'],
        del: ['admin', 'prodShouna:del']
      },
      rules: {
        serialFlag: [
          { required: true, message: '所属序列不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'shounaName', display_name: '类型' },
        { key: 'shounaItem', display_name: '项' },
        { key: 'shounaWidthdeephight', display_name: '宽深高' },
        { key: 'shounaFunc', display_name: '功能' },
        { key: 'shounaDoor', display_name: '门板' },
        { key: 'serialFlag', display_name: '所属序列' },
        { key: 'shounaRemark1', display_name: '预留1' },
        { key: 'shounaRemark2', display_name: '预留2' }
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
