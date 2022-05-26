<template>
  <div>



<!--    <div style="margin-top: 10px">-->
<!--      <el-card class="box-card">-->
<!--        <div slot="header" class="clearfix">-->
<!--          <span><el-link type="danger" disabled><h2>员工处罚参考项：</h2></el-link></span>-->
<!--        </div>-->
<!--        <div v-for="(punInfo, index) in punInfos" :key="index"  class="text item">-->
<!--          <el-link type="danger" disabled>{{punInfo}}</el-link>-->
<!--        </div>-->
<!--      </el-card>-->
<!--    </div>-->



    <div>
      <el-input size="small" v-model="addRE.eid" placeholder="添加员工id"
                prefix-icon="el-icon-plus" style="width: 150px"></el-input>
      <el-input size="small" v-model="addRE.sid" placeholder="添加员工对应的账套id"
                prefix-icon="el-icon-plus" style="width: 250px"></el-input>
      <el-button type="primary" icon="el-icon-plus" size="small" @click="addInfo">添加</el-button>
    </div>

    <template style="margin-top: 10px">
      <div style="margin-top: 10px">


        <el-table
            border
            :data="punData"
            style="width: 802px">
          <el-table-column
              prop="id"
              label="处罚对应ID"
              width="100">
          </el-table-column>
          <el-table-column
              prop="eid"
              label="员工ID"
              width="100">
          </el-table-column>
          <el-table-column
            prop="ename"
            label="员工姓名"
            width="150">

          </el-table-column>
          <el-table-column
              prop="sid"
              label="目前员工账套"
            width="150">
          </el-table-column>

          <el-table-column
              label="操作"
              width="200">
            <!-- 5-1 删除工资账套 拿到当前行数据 绑定点击事件 传行数据-->
            <template slot-scope="scope">
              <!-- 6-4 @click="showEditSalaryView(scope.row)">编辑  -->
              <el-button type="primary" size="mini" @click="showEditSalaryView(scope.row)">编辑</el-button>
              <el-button type="danger" size="mini" @click="deleteSalary(scope.row)">删除</el-button>
            </template>
          </el-table-column>

        </el-table>
      </div>

<!--      <div>-->
<!--        <el-dialog-->
<!--            :title=this.dialogTitle-->
<!--            width="30%">-->
<!--          <div>-->
<!--            &lt;!&ndash; 2-8 调整修改密码表单 &ndash;&gt;-->
<!--            <el-form :model="reword" status-icon  ref="reword" label-width="100px"-->
<!--                     class="demo-ruleForm">-->
<!--              <el-form-item label="请输入处罚对应id" prop="id">-->
<!--                <el-input type="text" v-model="reword.id" autocomplete="off"></el-input>-->
<!--              </el-form-item>-->
<!--              <el-form-item label="请输入员工id" prop="eid">-->
<!--                <el-input type="text" v-model="reword.eid" autocomplete="off"></el-input>-->
<!--              </el-form-item>-->
<!--              <el-form-item label="请输入目前员工帐套id" prop="sid">-->
<!--                <el-input type="password" v-model="reword.sid" autocomplete="off"></el-input>-->
<!--              </el-form-item>-->
<!--              <el-form-item>-->
<!--                <el-button type="primary" @click="submitForm('ruleForm')">提交</el-button>-->
<!--                <el-button @click="resetForm('ruleForm')">重置</el-button>-->
<!--              </el-form-item>-->
<!--            </el-form>-->
<!--          </div>-->
<!--        </el-dialog>-->
<!--      </div>-->

      <div style="display: flex;justify-content: flex-start;margin-top: 5px;">
        <el-pagination
            @size-change="sizeChange"
            @current-change="currentChange"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total" background>
        </el-pagination>
      </div>
    </template>

  </div>
</template>
<script>
export default {
  name: "PerEc",
  data() {
    return {
      currentPage: 1, // 1-2 当前页
      size: 10, // 1-2 每页显示条数
      total: 0, // 1-2 分页
      keywords: '',
      punInfos: [
        ' 月迟到3天内',
        ' 月迟到3-5天',
        ' 月绩效不合格'
      ],

      punData: [],
      addRE: {
        eid: '',
        sid: ''
      },
      reword: {
        id: 0,
        eid: 0,
        ename: '',
        sid: 0
      },
      dialogTitle: '',
      dialogVisible: false
    }
  },

  mounted() {
    this.initPunData()
  },

  methods: {

    showEditSalaryView(data) {
      this.dialogTitle = '编辑员工奖惩数据' // 设置标题

      this.reword.id = data.id
      this.reword.eid = data.eid
      this.reword.ename = data.ename
      this.reword.sid = data.sid

      this.dialogVisible = true // 打开对话框
    },

    deleteSalary(data) {
      this.$confirm('此操作将永久删除该[' + data.ename + ']员工奖惩数据, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteRequest('/personnel/ec/' + data.id).then(resp => {
          if (resp) {
            this.initPunData()
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },

    addInfo() {

      if (this.addRE.eid && this.addRE.sid ) {


        this.postRequest('/personnel/ec/', this.addRE).then(resp => {
          if (resp) {
            this.initPunData()

          }
        })
      } else {
        this.$message.error('字段不能为空！')
      }
    },

    // 1-3 分页-当前页
    currentChange(page) {
      this.currentPage = page
      this.initEmps()
    },
    // 1-4 分页-每页显示数量
    sizeChange(size) {
      this.size = size
      this.initEmps()
    },

    initPunData() {
      this.getRequest('/personnel/ec/?currentPage=' + this.currentPage + '&size=' + this.size).then(resp => {
        if (resp) {
          this.punData = resp.data
          this.total = resp.total
        }
      })
    }


  }
}
</script>

<style scoped>
.el-table .warning-row {
  background: oldlace;
}

.el-table .success-row {
  background: #f0f9eb;
}

.text {
  font-size: 14px;
}

.item {
  margin-bottom: 8px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both
}

.box-card {
  width: 250px;
}
</style>
