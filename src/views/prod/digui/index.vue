<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
                <label class="el-form-item-label">类型</label>
                <el-input v-model="query.prodName" clearable placeholder="类型" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">宽度</label>
                <el-input v-model="query.prodWidth" clearable placeholder="宽度" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">深高</label>
                <el-input v-model="query.prodDeepHight" clearable placeholder="深高" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">功能</label>
                <el-input v-model="query.prodFunc" clearable placeholder="功能" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">门板</label>
                <el-input v-model="query.prodDoor" clearable placeholder="门板" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">所属序列</label>
                <el-input v-model="query.serialFlag" clearable placeholder="所属序列" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="类型" prop="prodName">
            <el-input v-model="form.prodName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="宽度">
            <el-input v-model="form.prodWidth" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="深高">
            <el-input v-model="form.prodDeepHight" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="功能">
            <el-input v-model="form.prodFunc" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="门板">
            <el-input v-model="form.prodDoor" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="基础价格(JPY)">
            <el-input v-model="form.prodBasePrice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="功能价格(JPY)">
            <el-input v-model="form.prodFuncPrice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="所属序列" prop="serialFlag">
            <el-input v-model="form.serialFlag" style="width: 370px;" />
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
        <el-table-column prop="prodName" label="类型" />
        <el-table-column prop="prodWidth" label="宽度" />
        <el-table-column prop="prodDeepHight" label="深高" />
        <el-table-column prop="prodFunc" label="功能" />
        <el-table-column prop="prodDoor" label="门板" />
        <el-table-column prop="prodBasePrice" label="基础价格(JPY)" />
        <el-table-column prop="prodFuncPrice" label="功能价格(JPY)" />
        <el-table-column prop="serialFlag" label="所属序列" />
        <el-table-column v-if="checkPer(['admin','prodDigui:edit','prodDigui:del'])" label="操作" width="150px" align="center">
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
import crudProdDigui from '@/api/prodDigui'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { prodId: null, prodName: null, prodWidth: null, prodDeepHight: null, prodFunc: null, prodDoor: null, prodBasePrice: null, prodFuncPrice: null, serialFlag: null }
export default {
  name: 'ProdDigui',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '地柜', url: 'api/prodDigui', idField: 'prodId', sort: 'prodId,desc', crudMethod: { ...crudProdDigui }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'prodDigui:add'],
        edit: ['admin', 'prodDigui:edit'],
        del: ['admin', 'prodDigui:del']
      },
      rules: {
        prodName: [
          { required: true, message: '类型不能为空', trigger: 'blur' }
        ],
        serialFlag: [
          { required: true, message: '所属序列不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'prodName', display_name: '类型' },
        { key: 'prodWidth', display_name: '宽度' },
        { key: 'prodDeepHight', display_name: '深高' },
        { key: 'prodFunc', display_name: '功能' },
        { key: 'prodDoor', display_name: '门板' },
        { key: 'serialFlag', display_name: '所属序列' }
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
