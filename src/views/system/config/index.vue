<template>
  <div class="app-container">
    <div>
      <div
        v-if="showSearch"
        class="filter-container"
      >
        <el-form
          :inline="true"
          :model="listQuery"
          label-width="auto"
        >
          <el-form-item label="配置编码 ">
            <el-input
              v-model="listQuery.configId"
              placeholder="请输入配置编码 "
            />
          </el-form-item>
          <el-form-item label="模板编号 ">
            <el-input
              v-model="listQuery.configCode"
              placeholder="请输入模板编号 "
            />
          </el-form-item>
          <el-form-item label="模板名称 ">
            <el-input
              v-model="listQuery.configName"
              placeholder="请输入模板名称 "
            />
          </el-form-item>
          <el-form-item label="模板编号 ">
            <el-input
              v-model="listQuery.templateId"
              placeholder="请输入模板编号 "
            />
          </el-form-item>
          <el-form-item label="模板key ">
            <el-input
              v-model="listQuery.configKey"
              placeholder="请输入模板key "
            />
          </el-form-item>
          <el-form-item label="备注 ">
            <el-input
              v-model="listQuery.remarks"
              placeholder="请输入备注 "
            />
          </el-form-item>
        </el-form>
        <span
          slot="footer"
          class="dialog-footer"
        >
          <el-button
            v-waves
            type="success"
            icon="el-icon-search"
            @click="getList"
          >查询</el-button>
          <el-button
            v-waves
            icon="el-icon-download"
            @click="download"
          >导出</el-button>
          <el-button
            v-waves
            type="primary"
            icon="el-icon-plus"
            @click="handleCreate"
          >添加</el-button>
        </span>
      </div>
      <div class="table-container">
        <el-row
          :gutter="10"
          class="mb8"
        >
          <right-toolbar
            :show-search.sync="showSearch"
            :columns="columns"
            :default-hide-columns.sync="defaultHideColumns"
            @queryTable="getList"
          />
        </el-row>
        <el-table
          v-loading="listLoading"
          :data="list"
          size="mini"
          element-loading-text="Loading"
          fit
          border
          highlight-current-row
        >
          <el-table-column
            align="center"
            label="序号"
            width="95"
            sortable
          >
            <template slot-scope="scope">
              {{ (listQuery.pageIndex - 1 ) * listQuery.pageSize + scope.$index + 1 }}
            </template>
          </el-table-column>
          <el-table-column
            v-if="columns[0].visible"
            label="配置编码 "
            prop="configId"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[1].visible"
            label="模板编号 "
            prop="configCode"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[2].visible"
            label="模板名称 "
            prop="configName"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[3].visible"
            label="模板编号 "
            prop="templateId"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[4].visible"
            label="模板key "
            prop="configKey"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[5].visible"
            label="模板值 "
            prop="configValue"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[6].visible"
            label="模板类型 "
            prop="configType"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[7].visible"
            label="租户编号 "
            prop="tenantId"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[8].visible"
            label="备注 "
            prop="remarks"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[9].visible"
            label="状态"
            prop="validState"
            align="center"
          >
            <template slot-scope="scope">
              <el-tag :type="scope.row.validState | statusFilter">
                <span>{{ statusMap[scope.row.validState] }}</span>
              </el-tag>
            </template>
          </el-table-column>
          <el-table-column
            v-if="columns[10].visible"
            label="操作人工号 "
            prop="operatorId"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[11].visible"
            label="操作人姓名 "
            prop="operatorName"
            align="center"
            sortable
            :show-overflow-tooltip="true"
          />
          <el-table-column
            v-if="columns[12].visible"
            label="创建时间"
            align="center"
          >
            <template slot-scope="scope">
              <span>{{ scope.row.createTime }}</span>
            </template>
          </el-table-column>
          <el-table-column
            v-if="columns[13].visible"
            label="更新时间"
            align="center"
          >
            <template slot-scope="scope">
              <span>{{ scope.row.updateTime }}</span>
            </template>
          </el-table-column>
          <el-table-column
            align="center"
            label="操作"
            fixed="right"
            min-width="200px"
          >
            <template slot-scope="scope">
              <el-button
                v-waves
                size="mini"
                type="primary"
                icon="el-icon-edit"
                plain
                @click="handleUpdate(scope.row)"
              >编辑</el-button>
              <el-button
                v-waves
                size="mini"
                type="danger"
                icon="el-icon-delete"
                plain
                @click="handleDelete(scope.row)"
              >禁用</el-button>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页 -->
        <div class="page-footer">
          <pagination
            v-show="total>0"
            :total="total"
            :page.sync="listQuery.pageIndex"
            :limit.sync="listQuery.pageSize"
            style="float:right;"
            @pagination="getList"
          />
        </div>
      </div>

      <el-dialog
        :title="titleMap[dialogStatus]"
        :visible.sync="dialogVisible"
        width="40%"
        @close="handleDialogClose"
      >
        <el-form
          ref="dataForm"
          :model="form"
          :rules="rules"
          label-width="auto"
          class="demo-ruleForm"
        >
          <el-form-item
            v-if="true"
            label="配置编码 :"
            prop="configId"
          >
            <el-input
              v-model="form.configId"
              disabled
            />

          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板编号 :"
            prop="configCode"
          >
            <el-input
              v-model="form.configCode"
              placeholder="请输入模板编号 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板名称 :"
            prop="configName"
          >
            <el-input
              v-model="form.configName"
              placeholder="请输入模板名称 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板编号 :"
            prop="templateId"
          >
            <el-input
              v-model="form.templateId"
              placeholder="请输入模板编号 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板key :"
            prop="configKey"
          >
            <el-input
              v-model="form.configKey"
              placeholder="请输入模板key "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板值 :"
            prop="configValue"
          >
            <el-input
              v-model="form.configValue"
              placeholder="请输入模板值 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板类型 :"
            prop="configType"
          >
            <el-input
              v-model="form.configType"
              placeholder="请输入模板类型 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="租户编号 :"
            prop="tenantId"
          >
            <el-input
              v-model="form.tenantId"
              placeholder="请输入租户编号 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="备注 :"
            prop="remarks"
          >
            <el-input
              v-model="form.remarks"
              placeholder="请输入备注 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="状态 :"
            prop="validState"
          >
            <el-switch
              v-model="form.validState"
              :active-value="1"
              :inactive-value="0"
            />
          </el-form-item>
          <el-form-item
            v-if="false"
            label="操作人工号 :"
            prop="operatorId"
          >
            <el-input
              v-model="form.operatorId"
              placeholder="请输入操作人工号 "
            />
          </el-form-item>
          <el-form-item
            v-if="false"
            label="操作人姓名 :"
            prop="operatorName"
          >
            <el-input
              v-model="form.operatorName"
              placeholder="请输入操作人姓名 "
            />
          </el-form-item>
          <el-form-item
            v-if="false"
            label="创建时间 :"
            prop="createTime"
          >
            <el-date-picker
              v-model="form.createTime"
              type="date"
              placeholder="请输入创建时间 "
            />
          </el-form-item>
          <el-form-item
            v-if="false"
            label="更新时间 :"
            prop="updateTime"
          >
            <el-date-picker
              v-model="form.updateTime"
              type="date"
              placeholder="请输入更新时间 "
            />
          </el-form-item>
        </el-form>
        <span
          slot="footer"
          class="dialog-footer"
        >
          <el-button
            v-waves
            @click="dialogVisible = false"
          >取 消</el-button>
          <el-button
            v-waves
            type="primary"
            @click="submitForm"
          >确 定</el-button>
        </span>
      </el-dialog>

      <el-dialog
        :title="titleMap[dialogStatus]"
        :visible.sync="updateDialogVisible"
        width="40%"
        @close="handleDialogClose"
      >
        <el-form
          ref="updateDataForm"
          :model="updateForm"
          :rules="updateRules"
          label-width="auto"
          class="demo-ruleForm"
        >
          <el-form-item
            v-if="true"
            label="配置编码 :"
            prop="configId"
          >
            <el-input
              v-model="updateForm.configId"
              disabled
            />

          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板编号 :"
            prop="configCode"
          >
            <el-input
              v-model="updateForm.configCode"
              placeholder="请输入模板编号 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板名称 :"
            prop="configName"
          >
            <el-input
              v-model="updateForm.configName"
              placeholder="请输入模板名称 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板编号 :"
            prop="templateId"
          >
            <el-input
              v-model="updateForm.templateId"
              placeholder="请输入模板编号 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板key :"
            prop="configKey"
          >
            <el-input
              v-model="updateForm.configKey"
              placeholder="请输入模板key "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板值 :"
            prop="configValue"
          >
            <el-input
              v-model="updateForm.configValue"
              placeholder="请输入模板值 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="模板类型 :"
            prop="configType"
          >
            <el-input
              v-model="updateForm.configType"
              placeholder="请输入模板类型 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="租户编号 :"
            prop="tenantId"
          >
            <el-input
              v-model="updateForm.tenantId"
              placeholder="请输入租户编号 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="备注 :"
            prop="remarks"
          >
            <el-input
              v-model="updateForm.remarks"
              placeholder="请输入备注 "
            />
          </el-form-item>
          <el-form-item
            v-if="true"
            label="状态 :"
            prop="validState"
          >
            <el-switch
              v-model="updateForm.validState"
              :active-value="1"
              :inactive-value="0"
            />
          </el-form-item>
          <el-form-item
            v-if="false"
            label="操作人工号 :"
            prop="operatorId"
          >
            <el-input
              v-model="updateForm.operatorId"
              placeholder="请输入操作人工号 "
            />
          </el-form-item>
          <el-form-item
            v-if="false"
            label="操作人姓名 :"
            prop="operatorName"
          >
            <el-input
              v-model="updateForm.operatorName"
              placeholder="请输入操作人姓名 "
            />
          </el-form-item>
          <el-form-item
            v-if="false"
            label="创建时间 :"
            prop="createTime"
          >
            <el-date-picker
              v-model="updateForm.createTime"
              type="date"
              placeholder="请输入创建时间 "
            />
          </el-form-item>
          <el-form-item
            v-if="false"
            label="更新时间 :"
            prop="updateTime"
          >
            <el-date-picker
              v-model="updateForm.updateTime"
              type="date"
              placeholder="请输入更新时间 "
            />
          </el-form-item>
        </el-form>
        <span
          slot="footer"
          class="dialog-footer"
        >
          <el-button
            v-waves
            @click="updateDialogVisible = false"
          >取 消</el-button>
          <el-button
            v-waves
            type="primary"
            @click="submitForm"
          >确 定</el-button>
        </span>
      </el-dialog>
    </div>
  </div>
</template>

<script>
import {
  getConfigListPage,
  saveConfig,
  updateConfig,
  deleteById,
  download
} from '@/api/system/config'
import waves from '@/directive/waves'
import Pagination from '@/components/Pagination'

export default {
  components: { Pagination },
  directives: { waves },
  filters: {
    statusFilter(status) {
      const statusMap = {
        0: 'info',
        1: 'success'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
      showSearch: true,
      // 默认查询隐藏列
      defaultHideColumns: ['createTime', 'operatorId', 'createTime'],
      // 列信息
      columns: [
        { key: 0, value: 'configId', label: '配置编码 ', visible: true },
        { key: 1, value: 'configCode', label: '模板编号 ', visible: true },
        { key: 2, value: 'configName', label: '模板名称 ', visible: true },
        { key: 3, value: 'templateId', label: '模板编号 ', visible: true },
        { key: 4, value: 'configKey', label: '模板key ', visible: true },
        { key: 5, value: 'configValue', label: '模板值 ', visible: true },
        { key: 6, value: 'configType', label: '模板类型 ', visible: true },
        { key: 7, value: 'tenantId', label: '租户编号 ', visible: true },
        { key: 8, value: 'remarks', label: '备注 ', visible: true },
        { key: 9, value: 'validState', label: '状态 ', visible: true },
        { key: 10, value: 'operatorId', label: '操作人工号 ', visible: true },
        { key: 11, value: 'operatorName', label: '操作人姓名 ', visible: true },
        { key: 12, value: 'createTime', label: '创建时间 ', visible: true },
        { key: 13, value: 'updateTime', label: '更新时间 ', visible: true }
      ],
      dialogVisible: false,
      updateDialogVisible: false,
      list: [],
      listLoading: true,
      total: 0,
      listQuery: {
        pageIndex: 1,
        pageSize: 10,
        configId: undefined,
        configCode: undefined,
        configName: undefined,
        configKey: undefined,
        templateId: undefined,
        templateCode: undefined,
        templateName: undefined,
        templateKey: undefined,
        remarks: undefined
      },
      input: '',
      form: {
        configId: undefined, // 配置编码
        configCode: undefined, // 模板编号
        configName: undefined, // 模板名称
        templateId: undefined, // 模板编号
        configKey: undefined, // 模板key
        configValue: undefined, // 模板值
        configType: undefined, // 模板类型
        tenantId: undefined, // 租户编号
        remarks: undefined, // 备注
        validState: 1, // 状态
        operatorId: undefined, // 操作人工号
        operatorName: undefined, // 操作人姓名
        createTime: undefined, // 创建时间
        updateTime: undefined // 更新时间
      },
      updateForm: {
        configId: undefined, // 配置编码
        configCode: undefined, // 模板编号
        configName: undefined, // 模板名称
        templateId: undefined, // 模板编号
        configKey: undefined, // 模板key
        configValue: undefined, // 模板值
        configType: undefined, // 模板类型
        tenantId: undefined, // 租户编号
        remarks: undefined, // 备注
        validState: 1, // 状态
        operatorId: undefined, // 操作人工号
        operatorName: undefined, // 操作人姓名
        createTime: undefined, // 创建时间
        updateTime: undefined // 更新时间
      },
      dialogStatus: '',
      titleMap: {
        update: '编辑',
        create: '创建'
      },
      statusMap: { 1: '正常', 0: '停用' },
      rules: {
        configId: [],
        configCode: [
          { required: true, message: '请输入模板编号', trigger: 'blur' }
        ],
        configName: [
          { required: true, message: '请输入模板名称', trigger: 'blur' }
        ],
        templateId: [],
        configKey: [
          { required: true, message: '请输入模板key', trigger: 'blur' }
        ],
        configValue: [
          { required: true, message: '请输入模板值', trigger: 'blur' }
        ],
        configType: [
          { required: true, message: '请输入模板类型', trigger: 'blur' }
        ],
        tenantId: [],
        remarks: [],
        validState: [],
        operatorId: [],
        operatorName: [],
        createTime: [],
        updateTime: []
      },
      updateRules: {
        configId: [],
        configCode: [
          { required: true, message: '请输入模板编号', trigger: 'blur' }
        ],
        configName: [
          { required: true, message: '请输入模板名称', trigger: 'blur' }
        ],
        templateId: [],
        configKey: [
          { required: true, message: '请输入模板key', trigger: 'blur' }
        ],
        configValue: [
          { required: true, message: '请输入模板值', trigger: 'blur' }
        ],
        configType: [
          { required: true, message: '请输入模板类型', trigger: 'blur' }
        ],
        tenantId: [],
        remarks: [],
        validState: [],
        operatorId: [],
        operatorName: [],
        createTime: [],
        updateTime: []
      }
    }
  },
  created() {
    this.getList()
  },
  methods: {
    getList() {
      this.listLoading = true
      getConfigListPage(this.listQuery)
        .then((response) => {
          this.list = response.data.list
          this.total = response.data.total
          this.listLoading = false
        })
        .catch((err) => {
          this.listLoading = false
          console.log(err)
        })
    },
    download() {
      download(this.listQuery)
        .then((response) => {})
        .catch((err) => {
          console.log(err)
        })
    },
    handleCreate() {
      this.resetForm()
      this.dialogStatus = 'create'
      this.dialogVisible = true
    },
    handleUpdate(row) {
      this.updateForm = Object.assign({}, row)
      this.dialogStatus = 'update'
      this.updateDialogVisible = true
    },
    handleDelete(row) {
      const _this = this
      _this
        .$confirm('确定删除吗?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
        .then(function() {
          const id = row.configId
          deleteById(id).then((response) => {
            if (response.code === 200) {
              _this.getList()
              _this.$message.success(response.message)
            } else {
              _this.$message.error(response.message)
            }
          })
        })
    },
    submitForm() {
      const _this = this
      const refForm =
        _this.dialogStatus === 'create' ? 'dataForm' : 'updateDataForm'
      _this.$refs[`${refForm}`].validate((valid) => {
        if (valid) {
          this.$confirm('确定操作吗?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(function() {
            if (_this.dialogStatus === 'create') {
              saveConfig(_this.form)
                .then((response) => {
                  if (response.code === 200) {
                    _this.getList()
                    _this.$message.success(response.message)
                    _this.dialogVisible = false
                  } else {
                    _this.$message.error(response.message)
                  }
                })
                .catch((err) => {
                  console.log(err)
                })
            } else {
              updateConfig(_this.updateForm)
                .then((response) => {
                  if (response.code === 200) {
                    _this.getList()
                    _this.$message.success(response.message)
                    _this.updateDialogVisible = false
                  } else {
                    _this.$message.error(response.message)
                  }
                })
                .catch((err) => {
                  console.log(err)
                })
            }
          })
        }
      })
    },
    resetForm() {
      this.form = {
        configId: undefined, // 配置编码
        configCode: undefined, // 模板编号
        configName: undefined, // 模板名称
        templateId: undefined, // 模板编号
        configKey: undefined, // 模板key
        configValue: undefined, // 模板值
        configType: undefined, // 模板类型
        tenantId: undefined, // 租户编号
        remarks: undefined, // 备注
        validState: 1, // 状态
        operatorId: undefined, // 操作人工号
        operatorName: undefined, // 操作人姓名
        createTime: undefined, // 创建时间
        updateTime: undefined // 更新时间
      }
    },
    // 监听dialog关闭时的处理事件
    handleDialogClose() {
      const refForm =
        this.dialogStatus === 'create' ? 'dataForm' : 'updateDataForm'
      if (this.$refs[[`${refForm}`]]) {
        this.$refs[[`${refForm}`]].clearValidate() // 清除整个表单的校验
      }
    }
  }
}
</script>

<style  rel="stylesheet/scss" lang="scss" scoped>
.page-footer {
  margin-top: 20px;
  width: 100%;
  height: 50px;
}
.filter-container {
  padding-bottom: 10px;
  .filter-item {
    display: inline-block;
    vertical-align: middle;
    margin-bottom: 10px;
  }
}
</style>
