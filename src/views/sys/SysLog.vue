<template>
  <div>


    <el-input style="width: 300px;margin-right: 10px;"
              prefix-icon="el-icon-search"
              v-model="descr"
              placeholder="请输入相关功能进行搜索..."
              @keydown.enter.native="initLogs"
              clearable
              @clear="initLogs"
    ></el-input>
    <el-button type="primary" icon="el-icon-search" @click="initLogs">搜索
    </el-button>


    <div style="margin-top: 10px;">

      <!-- 2、表格；6、添加 loading -->
      <el-table
          size="mini"
          :data="systemLogs"
          stripe
          border>

        <el-table-column
            align="left"
            type="selection"
            width="55">
        </el-table-column>


        <el-table-column
            prop="createTime"
            label="操作时间"
            align="left"
            fixed
            width="120">
        </el-table-column>

        <el-table-column
            prop="descr"
            label="功能"
            align="left"
            fixed
            width="220">
        </el-table-column>
        <el-table-column
            prop="ipAddress"
            label="ip地址"
            align="left"
            width="120">
        </el-table-column>
        <el-table-column
            prop="operUser"
            label="操作人员工号"
            align="left"
            width="120">
        </el-table-column>
        <el-table-column
            prop="method"
            label="请求方法"
            align="left"
            width="120">
        </el-table-column>
        <el-table-column
            prop="url"
            label="请求url"
            align="left"
            width="180">
        </el-table-column>

        <el-table-column
            prop="params"
            label="参数"
            align="left"
            width="500">
        </el-table-column>

      </el-table>

      <!-- 1-1 分页组件 -->
      <div style="display: flex;justify-content: flex-end;margin-top: 5px;">
        <el-pagination
            @size-change="sizeChange"
            @current-change="currentChange"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total" background>
        </el-pagination>
      </div>
    </div>


  </div>
</template>

<script>


export default {
  name: "SysLog",


  data() {
    return {
      operUser: '',
      descr: '',
      systemLogs: [],
      currentPage: 1, // 1-2 当前页
      size: 10, // 1-2 每页显示条数
      total: 0 // 1-2 分页
    };
  },


  mounted() {
    this.initLogs()
  },


  methods: {
    // 15、分页 每页显示多少条 默认会把 size 传进来
    sizeChange(size) {
      this.size = size
      this.initLogs()
    },

    // 13、分页-当前页-currentPage 点击的时候自己会带过来
    currentChange(currentPage) {
      this.currentPage = currentPage // 16
      this.initLogs() // 18、调用方法
    },

    // 4、获取所有员工（分页）
    initLogs(type) {
      this.loading = true // 8、添加 loading
      // 30-11 定义高级搜索 url
      let url = '/system/log/?currentPage=' + this.currentPage + '&size=' + this.size
      if (this.descr) {
        url += '&name=' + this.descr
      }

      // 17、添加分页参数 ?currentPage='+this.currentPage+'&size='+this.size
      // 19、添加用户名搜索参数 +'&name='+this.empName,传参 根据条件搜索，不传参查询所有
      this.getRequest(url).then(resp => {
        // this.getRequest('/employee/basic/').then(resp => {
        if (resp) {
          this.systemLogs = resp.data
          this.total = resp.total // 12、分页
        }
      });
    }


  }
};
</script>

<style scoped>

</style>

