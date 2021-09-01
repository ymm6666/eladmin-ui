<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
                <label class="el-form-item-label">类型</label>
                <el-input v-model="query.kelinaName" clearable placeholder="类型" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">规格</label>
                <el-input v-model="query.kelinaStyle" clearable placeholder="规格" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">水槽</label>
                <el-input v-model="query.kelinaWatertank" clearable placeholder="水槽" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">宽深高</label>
                <el-input v-model="query.kelinaWidthdeephight" clearable placeholder="宽深高" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">所属序列</label>
                <el-input v-model="query.serialFlag" clearable placeholder="所属序列" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">预留1</label>
                <el-input v-model="query.kelinaRemark1" clearable placeholder="预留1" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">预留2</label>
                <el-input v-model="query.kelinaRemark2" clearable placeholder="预留2" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <date-range-picker
          v-model="query.kelinaBaseprice"
          start-placeholder="kelinaBasepriceStart"
          end-placeholder="kelinaBasepriceStart"
          class="date-item"
        />
        <date-range-picker
          v-model="query.kelinaFuncprice"
          start-placeholder="kelinaFuncpriceStart"
          end-placeholder="kelinaFuncpriceStart"
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
            <el-input v-model="form.kelinaName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="规格">
            <el-input v-model="form.kelinaStyle" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="水槽">
            <el-input v-model="form.kelinaWatertank" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="宽深高">
            <el-input v-model="form.kelinaWidthdeephight" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="标准价格(JPY)">
            <el-input v-model="form.kelinaBaseprice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="功能价格(JPY)">
            <el-input v-model="form.kelinaFuncprice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="所属序列" prop="serialFlag">
            <el-input v-model="form.serialFlag" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="预留1">
            <el-input v-model="form.kelinaRemark1" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="预留2">
            <el-input v-model="form.kelinaRemark2" style="width: 370px;" />
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
        <el-table-column prop="kelinaName" label="类型" />
        <el-table-column prop="kelinaStyle" label="规格" />
        <el-table-column prop="kelinaWatertank" label="水槽" />
        <el-table-column prop="kelinaWidthdeephight" label="宽深高" />
        <el-table-column prop="kelinaBaseprice" label="标准价格(JPY)" />
        <el-table-column prop="kelinaFuncprice" label="功能价格(JPY)" />
        <el-table-column prop="serialFlag" label="所属序列">
          <template slot-scope="scope">
            {{ dict.label.cd_serial[scope.row.serialFlag] }}
          </template>
        </el-table-column>
        <el-table-column prop="kelinaRemark1" label="预留1" />
        <el-table-column prop="kelinaRemark2" label="预留2" />
        <el-table-column v-if="checkPer(['admin','prodKelina:edit','prodKelina:del'])" label="操作" width="150px" align="center">
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
import crudProdKelina from '@/api/prodKelina'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { kelinaId: null, kelinaName: null, kelinaStyle: null, kelinaWatertank: null, kelinaWidthdeephight: null, kelinaBaseprice: null, kelinaFuncprice: null, serialFlag: null, kelinaRemark1: null, kelinaRemark2: null }
export default {
  name: 'ProdKelina',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['cd_serial'],
  cruds() {
    return CRUD({ title: '日本可丽娜台面', url: 'api/prodKelina', idField: 'kelinaId', sort: 'kelinaId,desc', crudMethod: { ...crudProdKelina }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'prodKelina:add'],
        edit: ['admin', 'prodKelina:edit'],
        del: ['admin', 'prodKelina:del']
      },
      rules: {
        serialFlag: [
          { required: true, message: '所属序列不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'kelinaName', display_name: '类型' },
        { key: 'kelinaStyle', display_name: '规格' },
        { key: 'kelinaWatertank', display_name: '水槽' },
        { key: 'kelinaWidthdeephight', display_name: '宽深高' },
        { key: 'serialFlag', display_name: '所属序列' },
        { key: 'kelinaRemark1', display_name: '预留1' },
        { key: 'kelinaRemark2', display_name: '预留2' }
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
