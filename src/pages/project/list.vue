<template>
  <div class="app-container">
    <div>
      <el-button @click="$router.push({ name: 'AddProject' })">添加项目</el-button>
    </div>
    <!--查询-->
    <div style="margin-top: 10px">
      <el-select v-model="queryForm.platform" placeholder="平台" clearable>
        <el-option v-for="platform in platforms" :key="platform.type" :label="platform.name" :value="platform.type" />
      </el-select>
      <el-button type="primary" class="el-icon-search" @click="onQueryBtnClick" />
    </div>
    <!-- 列表 -->
    <div style="margin-top: 10px">
      <el-table :data="projectList" highlight-current-row border>
        <el-table-column label="平台" align="center" width="200">
          <template scope="{ row }">
            {{ platforms.filter(p => p.type === row.platform)[0].name }}
          </template>
        </el-table-column>
        <el-table-column label="项目名称" align="center" prop="name" show-overflow-tooltip />
        <el-table-column label="项目描述" align="center" prop="description" show-overflow-tooltip />
        <el-table-column label="创建时间" align="center" width="200" show-overflow-tooltip>
          <template scope="{ row }">
            {{ `${row.creatorNickName} ${row.createTime}` }}
          </template>
        </el-table-column>
        <el-table-column label="操作" align="center" width="120">
          <template scope="{ row }">
            <el-button type="primary" class="el-icon-edit" @click="updateProject(row)" />
            <el-button type="danger" class="el-icon-delete" @click="deleteProject(row)" />
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!--分页-->
    <div>
      <pagination v-show="total>0" :total="total" :page.sync="queryForm.pageNum" :limit.sync="queryForm.pageSize" @pagination="fetchProjectList" />
    </div>
  </div>
</template>

<script>
import { platforms } from '@/utils/common'
import { getProjectList, deleteProject } from '@/api/project'
import Pagination from '@/components/Pagination'

export default {
  components: {
    Pagination
  },
  data() {
    return {
      platforms: platforms,
      projectList: [],
      total: 0,
      queryForm: {
        pageNum: 1,
        pageSize: 10,
        platform: null
      }
    }
  },
  created() {
    this.fetchProjectList()
  },
  methods: {
    onQueryBtnClick() {
      this.queryForm.pageNum = 1
      this.fetchProjectList()
    },
    updateProject(project) {
      this.$router.push({ name: 'UpdateProject', params: { projectId: project.id }})
    },
    deleteProject(project) {
      this.$confirm(`删除${project.name}`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        deleteProject(project.id).then(resp => {
          this.$notify.success(resp.msg)
          this.fetchProjectList()
        })
      })
    },
    fetchProjectList() {
      getProjectList(this.queryForm).then(resp => {
        this.projectList = resp.data.data
        this.total = resp.data.total
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
