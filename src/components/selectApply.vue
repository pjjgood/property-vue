<template>
  <div>
    <div style="margin-left: 10px;margin-top: 15px">
      <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 18px">
        <el-breadcrumb-item :to="{ path: '/staffMain/staffMessage' }">主页</el-breadcrumb-item>
        <el-breadcrumb-item>报修处理</el-breadcrumb-item>
        <el-breadcrumb-item>未处理报修信息</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <div style="width: 100%;margin-top: 5px">
      <el-table
        :data="apply"
        style="width: 100% ;font-size: 16px; box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04)"
        stripe>
        <el-table-column
          prop="apply_id"
          label="申请id"
          width="150"
          align="center"
          v-if="show"
        >
        </el-table-column>
        <el-table-column
          prop="realname"
          label="业主姓名"
          width="150"
          align="center"
        >
        </el-table-column>
        <el-table-column
          prop="apply_deso"
          label="报修申请"
          width="525"
          align="center"
        >
        </el-table-column>
        <el-table-column
          prop="build"
          label="楼号"
          width="100"
          align="center"
        >
        </el-table-column>
        <el-table-column
          prop="floor"
          label="层号"
          width="100"
          align="center"
        >
        </el-table-column>
        <el-table-column
          prop="room"
          label="房号"
          width="100"
          align="center"
        >
        </el-table-column>
          <el-table-column
            prop="apply_time"
            label="申请时间"
            width="200"
            align="center"
            :formatter="dateFormat"
          >
          </el-table-column>
            <el-table-column
              prop=""
              label="状态"
              width="250"
              align="center"
            >
              <template slot-scope="apply">
                {{ apply.row.status ? '未处理' : '已处理' }}
              </template>
        </el-table-column>

        <el-table-column
          label="操作"
          width="200"
          align="center"
        >
          <template slot-scope="apply">
            <el-button type="primary" round @click="applied(apply.row.apply_id)">已处理</el-button>
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
  </div>


</template>
<script>
  import axios from 'axios'
  import ElButton from "../../node_modules/element-ui/packages/button/src/button";
  export default{
    components: {ElButton},
    data() {
      return {
        apply:[],
        total:'',
        params: {
          page: '1',
          size: '10',
        }
      }
    },

    mounted(){
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
        var status=1;
        var url = '/api/selectApplyByStaff/'+status+"/"+this.params.page+"/"+this.params.size
        axios.get(url).then(res => {
          if(res.data=="未登录"){
            alert("您好，请登录")
            this.$router.push({path:'/login'})
          }
          if (res.data=="权限不足"){
            alert(res.data)
            this.$router.push({path:'/staffMain/noPermission'})
          }
          this.apply = res.data.applyList;
          this.total=res.data.total;
        })
      },
      dateFormat:function(row,column){
        var t=new Date(row.apply_time);//row 表示一行数据, updateTime 表示要格式化的字段名称
        return t.getFullYear()+"-"+(t.getMonth()+1)+"-"+t.getDate();
      },
      applied:function (applyId) {
        var url='api/updateApplyStatus'
        axios.post(url,{applyId:applyId}).then(res=>{
          if(res.data=="未登录"){
            alert("您好，请登录")
            this.$router.push({path:'/login'})
          }
          if (res.data=="权限不足"){
            alert(res.data)
            this.$router.push({path:'/staffMain/noPermission'})
          }
          if (res.data=="ok") {
            this.$message({
              message: '处理成功',
              type: 'success'
            });
            this.query();
          }
        })

      }
    }
  }

</script>
