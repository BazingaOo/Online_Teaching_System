<template>
    <div class="teacher-course">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/StudentContainer' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>课程问题</el-breadcrumb-item>
      </el-breadcrumb>
      </br>
      <el-table :data="msg" border style="width: 90%">
        <el-table-column prop="cId" label="课程编号" width="180">
          <template slot-scope="scope">
            <span>{{scope.row.cId}}</span>
          </template>
        </el-table-column>
        <el-table-column label="课程名称" width="180">
          <template slot-scope="scope">
            <span>{{ scope.row.cName }}</span>
          </template>
        </el-table-column>
        <el-table-column label="教师名称" width="180">
          <template slot-scope="scope">
            <span>{{ scope.row.tName }}</span>
          </template>
        </el-table-column>
        <el-table-column prop="cInfo" label="课程信息"></el-table-column>
        <el-table-column label="我的问题">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">查看</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </template>
  
  <script>
    export default {
      name: 'teacher-course',
      data() {
        return {
          msg: [],
        }
      },
      created: function () {
        this.selectAllCourse();
      },
      methods: {
        handleEdit(index, row) {
          this.$router.push({ path: 'MyQuestion', query: { cId: row.cId,tId:row.tId } })
        },
        selectAllCourse() {
          let that = this
          let sId = sessionStorage.getItem('sId');
          let params = { sId: sId }
          this.$axios.post("/api/selectStudentCourseBySid", this.$qs.stringify(params))
            .then(function (res) {
              if (res.data == '') {
                that.$message({
                  title: '系统提示',
                  message: '您当前还没有已选课程！',
                  showClose: true,
                  center: true,
                  type: 'success'
                })
                that.msg = []
              } else {
              that.msg = res.data;
                console.log(res.data);
              }
            }).catch(function (error) {
              console.log(error);
            });
        }
      },
    }
  
  </script>