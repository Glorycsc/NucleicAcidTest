<template>
  <div class="header">
    <!-- 折叠按钮 -->
    <div class="collapse-btn" @click="collapseChage">
      <i v-if="!collapse" class="el-icon-s-fold"></i>
      <i v-else class="el-icon-s-unfold"></i>
    </div>
    <div class="logo">核酸检测登记查询系统</div>
    <div class="header-right">
      <div class="header-user-con">
        <!-- 刷新-->
        <div class="btn-fullscreen" @click="handleRefresh">
          <el-tooltip effect="dark" :content="fullscreen?`刷新`:`刷新`" placement="bottom">
            <i class="el-icon-refresh"></i>
          </el-tooltip>
        </div>
        <!-- 全屏显示 -->
        <div class="btn-fullscreen" @click="handleFullScreen">
          <el-tooltip effect="dark" :content="fullscreen?`取消全屏`:`全屏`" placement="bottom">
            <i class="el-icon-rank"></i>
          </el-tooltip>
        </div>
        <!--选择导出日期-->
        <el-date-picker
            v-model="value2"
            type="daterange"
            align="right"
            unlink-panels
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            :picker-options="pickerOptions">
        </el-date-picker>

        <!-- 导出Excel按钮 -->
        <div class="btn-download" @click="handleDownload">
          <el-tooltip effect="dark" :content="fullscreen?`导出`:`导出`" placement="bottom">
            <i class="el-icon-download"></i>
          </el-tooltip>
        </div>
        <!-- 用户名下拉菜单 -->
        <el-dropdown class="user-name" trigger="click" @command="handleCommand">
                    <span class="el-dropdown-link">
                        {{ name }}
                        <i class="el-icon-caret-bottom"></i>
                    </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item divided command="logout">退出登录</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
    </div>
  </div>
</template>
<script>
import bus from './bus';
import axios from "axios";
import fileDownload from "js-file-download";
import {Toast} from "vant";
import UserInfo from "@/components/admin/UserInfo";

export default {
  data() {
    return {
      collapse: false,
      fullscreen: false,
      name: '',
      pickerOptions: {
        shortcuts: [{
          text: '最近一周',
          onClick(picker) {
            const end = new Date();
            const start = new Date();
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
            picker.$emit('pick', [start, end]);
          }
        }, {
          text: '最近一个月',
          onClick(picker) {
            const end = new Date();
            const start = new Date();
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
            picker.$emit('pick', [start, end]);
          }
        }, {
          text: '最近三个月',
          onClick(picker) {
            const end = new Date();
            const start = new Date();
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
            picker.$emit('pick', [start, end]);
          }
        }]
      },
      value1: '',
      value2: ''
    };
  },
  computed: {},
  methods: {
    checkLogin() {
      axios.post(
          '/checklogin'
      ).then((response) => {
        if (response.data.flag) {
          this.name = response.data.data.username;
        } else {
          this.$message.error(response.data.msg);
          this.$router.push('/admin/login');
        }
      })
          .catch(function (error) {
            console.log(error);
          });
    },
    logout() {
      this.axios.post(
          '/logout'
      ).then((response) => {
        if (response.data.flag) {
          this.$message.success(response.data.msg);
          this.$router.push('/admin/login');
        }
      })
          .catch(function (error) {
            console.log(error);
          });
    },
    // 用户名下拉菜单选择事件
    handleCommand(command) {
      if (command === 'logout') {
        this.logout();
      }
    },
    // 侧边栏折叠
    collapseChage() {
      this.collapse = !this.collapse;
      bus.$emit('collapse', this.collapse);
    },
    //下载Excel事件
    handleDownload() {
      // axios.post(postUrl, params, {responseType: 'arraybuffer'}).then(res => {
      //   fileDownload(res.data, 'xxx.xls')
      // })
      console.log(this.value2);
      Toast.fail(this.value2[0].toString());
    },
    //刷新页面获取最新数据
    handleRefresh() {
      // Toast.fail("正在获取最新数据");
      // this.$router.push('/admin/dashboard');
      this.$router.go(0);
      // window.location.reload()
    },
    // 全屏事件
    handleFullScreen() {
      let element = document.documentElement;
      if (this.fullscreen) {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        } else if (document.webkitCancelFullScreen) {
          document.webkitCancelFullScreen();
        } else if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen();
        } else if (document.msExitFullscreen) {
          document.msExitFullscreen();
        }
      } else {
        if (element.requestFullscreen) {
          element.requestFullscreen();
        } else if (element.webkitRequestFullScreen) {
          element.webkitRequestFullScreen();
        } else if (element.mozRequestFullScreen) {
          element.mozRequestFullScreen();
        } else if (element.msRequestFullscreen) {
          // IE11
          element.msRequestFullscreen();
        }
      }
      this.fullscreen = !this.fullscreen;
    }
  },
  mounted() {
    if (document.body.clientWidth < 1500) {
      this.collapseChage();
    }
  },
  created() {
    this.checkLogin()
  }
};
</script>
<style scoped>
.header {
  position: relative;
  box-sizing: border-box;
  width: 100%;
  height: 70px;
  font-size: 22px;
  color: #fff;
}

.collapse-btn {
  float: left;
  padding: 0 21px;
  cursor: pointer;
  line-height: 70px;
}

.header .logo {
  float: left;
  width: 250px;
  line-height: 70px;
}

.header-right {
  float: right;
  padding-right: 50px;
}

.header-user-con {
  display: flex;
  height: 70px;
  align-items: center;
}

.btn-fullscreen {
  transform: rotate(45deg);
  margin-right: 5px;
  font-size: 24px;
}

.btn-bell,
.btn-download {
  position: relative;
  width: 30px;
  height: 30px;
  text-align: center;
  border-radius: 15px;
  cursor: pointer;
  margin-right: 15px;
  margin-left: 5px;
}
.btn-fullscreen {
  position: relative;
  width: 30px;
  height: 30px;
  text-align: center;
  border-radius: 15px;
  cursor: pointer;
  margin-right: 15px;
}

.btn-bell-badge {
  position: absolute;
  right: 0;
  top: -2px;
  width: 8px;
  height: 8px;
  border-radius: 4px;
  background: #f56c6c;
  color: #fff;
}

.btn-bell .el-icon-bell {
  color: #fff;
}

.user-name {
  margin-left: 10px;
}

.user-avator {
  margin-left: 20px;
}

.user-avator img {
  display: block;
  width: 40px;
  height: 40px;
  border-radius: 50%;
}

.el-dropdown-link {
  color: #fff;
  cursor: pointer;
}

.el-dropdown-menu__item {
  text-align: center;
}
</style>
