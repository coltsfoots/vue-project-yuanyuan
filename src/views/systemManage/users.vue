<template>
	<div>
		<custom-form
      :formName="'usersForm'"
      :inline="true"
      :size="'small'"
      :showSerchIcon="true"
      :showResetBtn="true"
      :forms="forms"
      :handleSubmit="this.handleSubmit"
    >
			<template slot="button-slot">
				<el-button type="primary" @click.native="addUserDialogVisible = true">新增用户</el-button>
			</template>
		</custom-form>
		<custom-table :tableOptions="tableOptions">
			<template slot="handle-column">
				<el-table-column label="操作" width="200">
					<template slot-scope="scope">
            <div style="font-size: 30px;cursor: pointer;display: flex;justify-content: space-around;">
						  <tooltip-icon :iconClass="'view'" :content="'详情'"></tooltip-icon>
              <tooltip-icon :iconClass="'edit'" :content="'编辑'"></tooltip-icon>
              <el-dropdown style="font-size: 30px;">
                <span>
                  <svg-icon icon-class="list"></svg-icon>
                </span>
                <el-dropdown-menu slot="dropdown">
                  <el-dropdown-item v-if="scope.row.status === 0">审核</el-dropdown-item>
                  <el-dropdown-item v-else-if="scope.row.status === 1">停用</el-dropdown-item>
                  <el-dropdown-item v-else>启用</el-dropdown-item>
                  <el-dropdown-item>密码重置</el-dropdown-item>
                </el-dropdown-menu>
              </el-dropdown>
            </div>
					</template>
				</el-table-column>
			</template>
		</custom-table>
		<pagination
      :page="page"
      :layout="'sizes, prev, pager, next, jumper'"
      :prevText="'上一页'"
      :nextText="'下一页'"
      :totalPage="totalPage"
      @currentChange="getUsers({})"
    ></pagination>
    <template>
      <el-dialog
        :visible="addUserDialogVisible"
        title="新增用户"
      >
        <el-form>
          <el-form-item>
            <el-input v-model="value"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer">
          <el-button>确定</el-button>
        </div>
      </el-dialog>
    </template>
	</div>
</template>

<script>
import CustomForm from 'vue-simple-custom-form'
import CustomTable from 'vue-simple-custom-table'
import Pagination from 'vue-simple-custom-pagination'
import TooltipIcon from '@/components/TooltipIcon/index'
import { getUsersList, getTenant } from '@/api/system/users'
export default {
  name: 'Users',
  components: {
    CustomForm,
    CustomTable,
    Pagination,
    TooltipIcon
  },
  data() {
    return {
      forms: [
        {
          label: '用户姓名',
          prop: 'userName'
        },
        {
          label: '所属商户',
          prop: 'tenant',
          itemType: 'select',
          selectFetch: getTenant
        },
        {
          label: '手机号',
          prop: 'tel'
        },
        {
          label: '状态',
          prop: 'status',
          itemType: 'select',
          options: [
            {
              label: '待审核',
              value: 0
            },
            {
              label: '启用',
              value: 1
            },
            {
              label: '停用',
              value: 2
            }
          ]
        }
      ],
      tableOptions: {
        columns: [
          {
            label: '用户姓名',
            prop: 'userName'
          },
          {
            label: '手机号码',
            prop: 'tel'
          },
          {
            label: '所属商户',
            prop: 'tenant'
          },
          {
            label: '状态',
            prop: 'status',
            formatter: (row) => {
              return row.status === 0 ? '待审核' : row.status === 1 ? '启用' : '停用'
            }
          },
          {
            label: '最后登录时间',
            prop: 'lastLoginTime'
          }
        ],
        dataSource: null
      },
      page: {
        pageIndex: 1,
        pageSize: 20
      },
      totalPage: null,
      addUserDialogVisible: false,
      value: ''
    }
  },
  created() {
    this.getUsers({})
  },
  methods: {
    getUsers(formParams) {
      getUsersList(formParams, this.page).then(res => {
        this.tableOptions.dataSource = res.data
        this.totalPage = res.total
      })
    },
    handleSubmit(formParams) {
      console.log(formParams)
      console.log(this)
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
