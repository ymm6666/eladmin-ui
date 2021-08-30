<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
                <label class="el-form-item-label">类型</label>
                <el-input v-model="query.zhuangshiName" clearable placeholder="类型" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">项</label>
                <el-input v-model="query.zhuangshiItem" clearable placeholder="项" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">宽深高</label>
                <el-input v-model="query.zhuangshiWidthdeephight" clearable placeholder="宽深高" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">功能</label>
                <el-input v-model="query.zhuangshiFunc" clearable placeholder="功能" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">门板</label>
                <el-input v-model="query.zhuangshiDoor" clearable placeholder="门板" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">所属序列</label>
                <el-input v-model="query.serialFlag" clearable placeholder="所属序列" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">预留1</label>
                <el-input v-model="query.zhuangshiRemark1" clearable placeholder="预留1" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">预留2</label>
                <el-input v-model="query.zhuangshiRemark2" clearable placeholder="预留2" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <date-range-picker
          v-model="query.zhuangshiBaseprice"
          start-placeholder="zhuangshiBasepriceStart"
          end-placeholder="zhuangshiBasepriceStart"
          class="date-item"
        />
        <date-range-picker
          v-model="query.zhuangshiFuncprice"
          start-placeholder="zhuangshiFuncpriceStart"
          end-placeholder="zhuangshiFuncpriceStart"
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
            <el-input v-model="form.zhuangshiName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="项">
            <el-input v-model="form.zhuangshiItem" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="宽深高">
            <el-input v-model="form.zhuangshiWidthdeephight" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="功能">
            <el-input v-model="form.zhuangshiFunc" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="门板">
            <el-input v-model="form.zhuangshiDoor" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="标准价格(JPY)">
            <el-input v-model="form.zhuangshiBaseprice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="功能价格(JPY)">
            <el-input v-model="form.zhuangshiFuncprice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="所属序列" prop="serialFlag">
            <el-input v-model="form.serialFlag" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="预留1">
            <el-input v-model="form.zhuangshiRemark1" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="预留2">
            <el-input v-model="form.zhuangshiRemark2" style="width: 370px;" />
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
        <el-table-column prop="zhuangshiName" label="类型" />
        <el-table-column prop="zhuangshiItem" label="项" />
        <el-table-column prop="zhuangshiWidthdeephight" label="宽深高" />
        <el-table-column prop="zhuangshiFunc" label="功能" />
        <el-table-column prop="zhuangshiDoor" label="门板">
          <template slot-scope="scope">
            {{ dict.label.cd_class[scope.row.zhuangshiDoor] }}
          </template>
        </el-table-column>
        <el-table-column prop="zhuangshiBaseprice" label="标准价格(JPY)" />
        <el-table-column prop="zhuangshiFuncprice" label="功能价格(JPY)" />
        <el-table-column prop="serialFlag" label="所属序列">
          <template slot-scope="scope">
            {{ dict.label.cd_serial[scope.row.serialFlag] }}
          </template>
        </el-table-column>
        <el-table-column prop="zhuangshiRemark1" label="预留1" />
        <el-table-column prop="zhuangshiRemark2" label="预留2" />
        <el-table-column v-if="checkPer(['admin','prodZhuangshi:edit','prodZhuangshi:del'])" label="操作" width="150px" align="center">
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
import crudProdZhuangshi from '@/api/prodZhuangshi'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'

const defaultForm = { zhuangshiId: null, zhuangshiName: null, zhuangshiItem: null, zhuangshiWidthdeephight: null, zhuangshiFunc: null, zhuangshiDoor: null, zhuangshiBaseprice: null, zhuangshiFuncprice: null, serialFlag: null, zhuangshiRemark1: null, zhuangshiRemark2: null }
export default {
  name: 'ProdZhuangshi',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['cd_class', 'cd_serial'],
  cruds() {
    return CRUD({ title: '装饰板维护', url: 'api/prodZhuangshi', idField: 'zhuangshiId', sort: 'zhuangshiId,desc', crudMethod: { ...crudProdZhuangshi }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'prodZhuangshi:add'],
        edit: ['admin', 'prodZhuangshi:edit'],
        del: ['admin', 'prodZhuangshi:del']
      },
      rules: {
        serialFlag: [
          { required: true, message: '所属序列不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'zhuangshiName', display_name: '类型' },
        { key: 'zhuangshiItem', display_name: '项' },
        { key: 'zhuangshiWidthdeephight', display_name: '宽深高' },
        { key: 'zhuangshiFunc', display_name: '功能' },
        { key: 'zhuangshiDoor', display_name: '门板' },
        { key: 'serialFlag', display_name: '所属序列' },
        { key: 'zhuangshiRemark1', display_name: '预留1' },
        { key: 'zhuangshiRemark2', display_name: '预留2' }
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
