<template>
  <div>
    <div class="crumbs">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item>
          <i class="el-icon-s-custom"></i> 人员信息
        </el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <div class="container">
      <div class="handle-box">
        <el-input v-model="query.idcardnum" placeholder="身份证号" class="handle-input mr10"></el-input>
        <el-button type="primary" icon="el-icon-search" @click="getData(true)">搜索</el-button>
      </div>
      <el-table
          :data="tableData"
          border
          class="table"
          ref="multipleTable"
          header-cell-class-name="table-header"
      >
        <el-table-column prop="tname" label="人员姓名" width="100" align="center"></el-table-column>
        <!--        刷新这一条数据-->
        <el-table-column label="性别" width="155" align="center">
          <template slot-scope="scope">
            <!--            <el-tag size="medium" v-if='scope.row.sex===1'>男</el-tag>-->
            <!--            <el-tag size="medium" v-else-if='scope.row.sex===2'>女</el-tag>-->
            <el-button type="primary" disabled='scope.row.sex===1' onclick="changeState(scope.$index,scope.row)">已检测</el-button>
          </template>
        </el-table-column>
        <el-table-column prop="idcardnum" label="身份证号" align="center"></el-table-column>
        <el-table-column prop="phonenum" label="联系电话" align="center"></el-table-column>
        <el-table-column label="详细地址" align="center">
          <template slot-scope="scope">
            {{ scope.row.area + '/' + scope.row.address }}
          </template>
        </el-table-column>
      </el-table>
      <div class="pagination">
        <el-pagination
            :current-page="currentPage"
            :page-sizes="[2,20, 30, 50, 100]"
            :page-size="20"
            background
            layout="total, sizes, prev, pager, next"
            :total="total"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
        ></el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import {Toast} from "vant";

export default {
  name: 'ordertable',
  data() {
    return {
      query: {
        idcardnum: null
      },
      tableData: [],
      pageSize: 20,
      currentPage: 1,
      total: 50,
    };
  },
  created() {
    this.getData(true)
  },
  methods: {
    getData(isSearch) {
      Toast.fail("diaoyongchdnegogn");
      if (isSearch) {
        this.axios.post(
            '/admin/getAllUserInfo', this.qs.stringify({
                  pageNum: this.currentPage,
                  pageSize: this.pageSize,
                  idcardnum: this.query.idcardnum
                }
            )).then((response) => {
          if (response.data.flag) {
            this.tableData = response.data.data.list;
            this.total = response.data.data.total;
          } else {
            this.tableData = response.data.data;
          }
        })
            .catch(function (error) {
              console.log(error);
            });
      } else {
        this.axios.post(
            '/admin/getAllUserInfo', this.qs.stringify({
                  pageNum: this.currentPage,
                  pageSize: this.pageSize,
                  // idcardnum: isSearch ? this.query.idcardnum : null
                }
            )).then((response) => {
          if (response.data.flag) {
            this.tableData = response.data.data.list;
            this.total = response.data.data.total;
          } else {
            this.tableData = response.data.data;
          }
        })
            .catch(function (error) {
              console.log(error);
            });
      }
    },
    // 分页导航
    handleSizeChange(val) {
      this.pageSize = val;
      this.getData(true)
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.getData(true)

    },
    //修改一条信息状态
    changeState(index,row) {
//手动改变表单值
      this.tableData[index].sex ===1?this.tableData[index].sex=2:this.tableData[index].sex=1;
//利用vue的$set方法进行更新表格数据
      this.$set(this.tableData, index, row);
    }
  }
};
</script>

<style scoped>
.handle-box {
  margin-bottom: 20px;
}

.handle-select {
  width: 180px;
}

.handle-input {
  width: 300px;
  display: inline-block;
}

.table {
  width: 100%;
  font-size: 14px;
}

.red {
  color: #ff0000;
}

.mr10 {
  margin-right: 10px;
}

.table-td-thumb {
  display: block;
  margin: auto;
  width: 40px;
  height: 40px;
}
</style>
