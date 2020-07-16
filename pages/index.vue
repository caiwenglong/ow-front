<template>
  <div class="page-home">
    <div class="page-container">
      <div class="page-logo">
        <img src="../assets/images/logo.png" alt="" />
      </div>
      <div class="">
        <div class="search-input">
          <el-autocomplete
            v-model="state"
            placeholder="请输入网站名称并回车搜索"
            suffix-icon="el-icon-search"
            :fetch-suggestions="querySearchAsync"
            :trigger-on-focus="false"
            :highlight-first-item="true"
            @select="handleSelect"
          >
            <template slot-scope="{ item }">
              <a class="ow-item" :href="item.url">
                <span class="ow-name" v-html="item.name" />
                <span class="ow-name">：</span>
                <span class="ow-addr">{{ item.url }}</span>
                <span class="ow-tip-direct">直接跳转到该网站的官网</span>
              </a>
            </template>
          </el-autocomplete>
        </div>
        <div class="search-btn">
          <el-button type="primary">搜一下</el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'
  export default {
    data() {
      return {
        state: '',
        websiteList: [],
        timeout: null
      }
    },
    methods: {
      querySearchAsync(queryString, cb) {
        axios
          .get(
            `http://localhost:8001/uInterface/tb-website/find/${queryString}/1/10`
          )
          .then(res => {
            if (res.data.isSuccess && res.data.code === 'OW20000') {
              this.websiteList = res.data.data.websiteArrayList
              debugger
              cb(this.websiteList)
            }
          })
      },
      handleSelect(item) {
        console.log(item)
      }
    }
  }
</script>

<style scoped lang="scss">
  .page-home {
    width: 100%;
    height: 100%;
    position: relative;
  }
  .page-container {
    width: 50%;
    position: absolute;
    top: 15%;
    left: 50%;
    margin-left: -25%;
    text-align: center;
  }
  .page-logo {
    width: 150px;
    margin: 0 auto;
    img {
      width: 100%;
    }
  }
  .search-input {
    width: 100%;
    .el-autocomplete {
      display: block;
    }
  }
  .search-btn {
    margin-top: 42px;
  }
  .ow-item {
    color: #a7aaaf;
    text-decoration: none;
  }
  .el-autocomplete-suggestion {
    li {
      position: relative;
    }
    .ow-tip-direct {
      position: absolute;
      right: 20px;
    }
  }
</style>
