<template>
  <div class="app-container">
    <el-form :inline="true" class="demo-form-inline">
      <el-form-item label="課程名稱">
        <el-input v-model="username" />
      </el-form-item>
      <el-form-item>
        <el-button type="warning" @click="onSubmit">查询</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="success" @click="adduser">建立課程</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="list"
      border
      fit
      highlight-current-row
    >
      <!-- <el-table-column align="center" label="序号" width="95">
        <template slot-scope="scope">
          {{ scope.$index + 1 }}
        </template>
      </el-table-column> -->
      <el-table-column align="center" label="課程ID" width="95">
        <template slot-scope="scope">
          {{ scope.row.courseId }}
        </template>
      </el-table-column>
      <el-table-column align="center" label="課程名稱">
        <template slot-scope="scope">
          {{ scope.row.courseName }}
        </template>
      </el-table-column>
      <el-table-column align="center" label="價格" width="95">
        <template slot-scope="scope">
          {{ scope.row.coursePrice }}
        </template>
      </el-table-column>
      <el-table-column align="center" label="授課人數">
        <template slot-scope="scope">
          {{ scope.row.taughtPeople }}
        </template>
      </el-table-column>
      <el-table-column align="center" label="開課日期" width="95">
        <template slot-scope="scope">
          {{ scope.row.startDate }}
        </template>
      </el-table-column>
      <!-- <el-table-column align="center" label="手机号">
        <template slot-scope="scope">
          {{ scope.row.userTel }}
        </template>
      </el-table-column>
      <el-table-column align="center" label="注册日期" width="160">
        <template slot-scope="scope">
          {{ scope.row.registerDate }}
        </template>
      </el-table-column>
      <el-table-column align="center" label="用户状态">
        <template slot-scope="scope">
          {{ scope.row.tState.stateName }}
        </template>
      </el-table-column> -->

      <el-table-column label="操作" align="center" width="230" class-name="small-padding fixed-width">
        <template slot-scope="scope">
          <el-button type="primary" size="mini" @click="edituser(scope.row.userId)">
            编辑
          </el-button>
          <el-button type="danger" size="mini" @click="deluser(scope.row.userId)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <pagination v-show="total>0" :total="total" :page.sync="listQuery.page" :limit.sync="listQuery.limit" @pagination="getList" />

  </div>
</template>
<script>
import Pagination from '@/components/Pagination' // secondary package based on el-pagination
export default {
  components: { Pagination },
  data() {
    return {
      username: '',
      total: 0,
      listQuery: {
        page: 1,
        limit: 10
      },
      list: [{
        // 'tState': {
        //   'stateId': null,
        //   'stateName': '认证中'
        // },
        'courseId': 1,
        'courseName': '30天教你如何割韭菜',
        'taughtPeople': 666666,
        'coursePrice': 888888,
        // 'password': 666666,
        // 'encryptedProblem': '123',
        'startDate': '2077-07-07T07:17:10.000+0000'
        // 'userTel': '15566666626',
        // 'registerDate': '2021-04-02T07:17:10.000+0000',
        // 'iconUrl': null,
        // 'stateId': 1
      }
      ]
    }
  },
  created() {
    // this.fetchData()
    this.getList()
  },

  methods: {
    onSubmit() {
      var vm = this
      this.axios({
        method: 'GET',
        url: 'http://localhost:8081/admin/searchUser?pageNum=' + vm.listQuery.page + '&pageSize=' + vm.listQuery.limit + '&username=' + vm.username
      }).then(function(resp) {
        vm.total = resp.data.total // 讲pageInfo中的total放到vm的total
        vm.list = resp.data.list
      })
    },
    getList() {
      var vm = this
      this.axios({
        method: 'GET',
        url: 'http://localhost:8081/admin/pageInfo?pageNum=' + vm.listQuery.page + '&pageSize=' + vm.listQuery.limit
      }).then(function(resp) {
        vm.total = resp.data.total // 讲pageInfo中的total放到vm的total
        vm.list = resp.data.list
      })
    },
    adduser() {
      this.$router.push('/adduser/index')
    },
    edituser(id) {
      this.$router.push('/edituser/index/' + id)
    },
    deluser(id) {
      var vm = this
      this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        vm.axios({
          method: 'GET',
          url: 'http://localhost:8081/admin/deleteUser/' + id
        }).then(function(resp) {
          if (resp.data === 'success') {
            vm.$message({
              message: '删除成功',
              type: 'success'
            })
            vm.getList()
          }
          // eslint-disable-next-line handle-callback-err
        }).catch(function(error) {
          vm.$message.error('删除失败')
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    // 获视频列表
    fetchData() {
      var vm = this
      this.axios({
        method: 'GET',
        url: 'http://localhost:8081/admin/list'
      }).then(function(resp) {
        vm.list = resp.data
        console.log(resp.data)
      })
    }
  }
}
</script>
