<template>
  <div style="width: 100%">
    <div style="margin-left: 10px;margin-top: 15px">
      <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 18px">
        <el-breadcrumb-item :to="{ path: '/staffMain/staffMessage' }">主页</el-breadcrumb-item>
        <el-breadcrumb-item>员工管理</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <div style="width: 100%;">
      <span style="font-size: 20px;color: darkgray;
      margin:auto;height: 50px;line-height: 50px">员工信息</span>

      <el-table
        :data="staff"
        style="width: 100% ;font-size: 16px; box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04)"
        stripe>

        <el-table-column
          prop="realName"
          label="员工姓名"
          width="400"
          align="center"
        >
        </el-table-column>
        <el-table-column
          prop="staffNumber"
          label="员工工号"
          width="400"
          align="center"
        >
        </el-table-column>
        <el-table-column
          prop="roleName"
          label="员工职责"
          width="400"
          align="center"
        >
          <template slot-scope="staff">
            {{ staff.row.roleName=="admin"? '超级管理员' : '物业管家' }}
          </template>
        </el-table-column>
        <el-table-column
          label="操作"
          width="423"
          align="center"
        >
          <template slot-scope="staff">
            <el-button type="danger" round @click="deleteStaff(staff.row.staffId)">删除</el-button>
            <el-button type="primary" round @click="toUpdtaeStaff(staff.row.staffId)">修改</el-button>
          </template>
        </el-table-column>

      </el-table>
      <br/>
      <el-pagination
        background
        layout="prev, pager, next"
        :page-size="this.params.size"
        v-on:current-change="changePage"
        :total="total"
        :current-page="this.params.page">
      </el-pagination>

    </div>
    <el-button  type="primary" round style="font-size: 15px;margin-top: 20px;" @click="insertStaff()">新增员工</el-button>
  </div>


</template>
<script>
  import axios from 'axios'
  import ElButton from "../../node_modules/element-ui/packages/button/src/button";
  export default{
    components: {ElButton},
    data() {
      return {
        staff:[],
        params: {
          page: '1',
          size: '10',
        },
        staffName:'',
      }
    },

    mounted(){
      // 查员工信息
      this.query();
    },
    methods:{
      tableRowClassName({row, rowIndex}) {
        if (rowIndex === 1) {
          return 'warning-row';
        } else if (rowIndex === 3) {
          return 'success-row';
        }
        return '';
      },
      changePage:function (page) {
        this.params.page=page
        this.query();
      },
      query:function () {
        var url = '/api/findStaff'
        axios.post(url,{currentPage:this.params.page,pageSize:this.params.size}).then(res => {
          if(res.data=="未登录"){
            alert("您好，请登录")
            this.$router.push({path:'/login'})
          }
          if (res.data=="权限不足"){
            alert(res.data)
            this.$router.push({path:'/staffMain/noPermission'})
          }
          this.staff = res.data.staffs;
          this.total=res.data.total;
        })
      },
      deleteStaff:function (staffId) {
        this.$confirm('此操作将永久删除该记录, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          var url='/api/delStaff'
          axios.post(url, {staffId: staffId}).then(res => {
            if(res.data=="未登录"){
              alert("您好，请登录")
              this.$router.push({path:'/login'})
            }
            if (res.data=="权限不足"){
              alert(res.data)
              this.$router.push({path:'/staffMain/noPermission'})
            }
            if (res.data =="success") {
              this.query();
            } else {
              alert("删除失败")
            }
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        })
      },
      toUpdtaeStaff:function (staffId) {
        this.$router.push({path:'/staffMain/permissionAssignment/'+staffId})
      },
      insertStaff:function () {
        this.$router.push({path:'/staffMain/insertStaff'})
      }
    }
  }

</script>
