<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
                  <el-select v-model="query.serialFlag" clearable filterable
                             placeholder="请选择" style="width: 185px;" class="filter-item" @change="crud.toQuery" >
                    <el-option
                            v-for="item in dict.cd_serial"
                            :key="item.id"
                            :label="item.label"
                            :value="item.value" />
                  </el-select>
                <label class="el-form-item-label">类型</label>
                <el-input v-model="query.diguiName" clearable placeholder="类型" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">宽度</label>
                <el-input v-model="query.diguiWidth" clearable placeholder="宽度" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">深高</label>
                <el-input v-model="query.diguiDeephight" clearable placeholder="深高" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">功能</label>
                <el-input v-model="query.diguiFunc" clearable placeholder="功能" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
                <label class="el-form-item-label">门板</label>
                <el-input v-model="query.diguiDoor" clearable placeholder="门板" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <date-range-picker
          v-model="query.diguiBaseprice"
          start-placeholder="diguiBasepriceStart"
          end-placeholder="diguiBasepriceStart"
          class="date-item"
        />
        <date-range-picker
          v-model="query.diguiFuncprice"
          start-placeholder="diguiFuncpriceStart"
          end-placeholder="diguiFuncpriceStart"
          class="date-item"
        />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="所属序列" prop="serialFlag">
            <el-select v-model="form.serialFlag" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.cd_serial"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="类型" prop="diguiName">
            <el-input v-model="form.diguiName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="宽度">
            <el-input v-model="form.diguiWidth" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="深高">
            <el-input v-model="form.diguiDeephight" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="功能">
            <el-input v-model="form.diguiFunc" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="门板">
            <el-radio v-model="form.diguiDoor" v-for="item in dict.cd_class" :key="item.id" :label="item.value">{{ item.label }}</el-radio>
          </el-form-item>
          <el-form-item label="标准价格(JPY)">
            <el-input v-model="form.diguiBaseprice" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="功能价格(JPY)">
            <el-input v-model="form.diguiFuncprice" style="width: 370px;" />
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
        <el-table-column prop="serialFlag" label="所属序列">
          <template slot-scope="scope">
            {{ dict.label.cd_serial[scope.row.serialFlag] }}
          </template>
        </el-table-column>
        <el-table-column prop="diguiName" label="类型" />
        <el-table-column prop="diguiWidth" label="宽度" />
        <el-table-column prop="diguiDeephight" label="深高" />
        <el-table-column prop="diguiFunc" label="功能" />
        <el-table-column prop="diguiDoor" label="门板">
          <template slot-scope="scope">
            {{ dict.label.cd_class[scope.row.diguiDoor] }}
          </template>
        </el-table-column>
        <el-table-column prop="diguiBaseprice" label="标准价格(JPY)" />
        <el-table-column prop="diguiFuncprice" label="功能价格(JPY)" />
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

const defaultForm = { serialFlag: null, diguiId: null, diguiName: null, diguiWidth: null, diguiDeephight: null, diguiFunc: null, diguiDoor: null, diguiBaseprice: null, diguiFuncprice: null }
export default {
  name: 'ProdDigui',
  components: { pagination, crudOperation, rrOperation, udOperation },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['cd_serial', 'cd_class'],
  cruds() {
    return CRUD({ title: '地柜', url: 'api/prodDigui', idField: 'diguiId', sort: 'diguiId,desc', crudMethod: { ...crudProdDigui }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'prodDigui:add'],
        edit: ['admin', 'prodDigui:edit'],
        del: ['admin', 'prodDigui:del']
      },
      rules: {
        serialFlag: [
          { required: true, message: '所属序列不能为空', trigger: 'blur' }
        ],
        diguiName: [
          { required: true, message: '类型不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'serialFlag', display_name: '所属序列' },
        { key: 'diguiName', display_name: '类型' },
        { key: 'diguiWidth', display_name: '宽度' },
        { key: 'diguiDeephight', display_name: '深高' },
        { key: 'diguiFunc', display_name: '功能' },
        { key: 'diguiDoor', display_name: '门板' }
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
