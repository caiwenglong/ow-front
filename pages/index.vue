<template>
  <div class="page-home">
    <div class="page-container">
      <div class="page-logo">
        <img src="../assets/images/logo.png" alt="" />
      </div>
      <div class="">
        <div class="search-input">
          <el-autocomplete
            v-model="keyword"
            placeholder="请输入网站名称并回车搜索"
            suffix-icon="el-icon-search"
            :fetch-suggestions="querySearchAsync"
            :trigger-on-focus="true"
            :highlight-first-item="true"
            :hide-loading="true"
            :value-key="valueKey"
            @select="handleSelect"
          >
            <template slot-scope="{ item }">
              <template v-if="item.isEmptyTip">
                <span class="ow-name" v-html="item.value" />
              </template>
              <template v-else>
                <span class="ow-item">
                  <span class="ow-name" v-html="item.name" />
                  <span class="ow-name">：</span>
                  <span class="ow-addr">{{ item.url }}</span>
                  <span v-if="item.url" class="ow-tip-direct">
                    直接跳转到该网站的官网
                  </span>
                </span>
              </template>
            </template>
          </el-autocomplete>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'
  export default {
    data() {
      const emptyResponseTip = [
        { value: '没有搜索到数据，请输入其他名称', isEmptyTip: true }
      ]
      return {
        keyword: '',
        valueKey: 'name',
        websiteList: [],
        timeout: null,
        emptyResponseTip
      }
    },
    methods: {
      querySearchAsync(queryString, cb) {
        const _this = this
        axios
          .get(
            `http://localhost:8001/uInterface/tb-website/find/${queryString}/1/10`
          )
          .then(res => {
            if (res.data.isSuccess && res.data.code === 'OW20000') {
              _this.websiteList = res.data.data.websiteArrayList
              if (_this.websiteList.length > 0) {
                cb(_this.websiteList)
              } else {
                cb(_this.emptyResponseTip)
              }
            }
          })
      },
      handleSelect(item) {
        this.keyword = item.name.replace(/<\/?.+?\/?>/g, '')
        console.log(item.url)
        window.open(item.url, '_blank')
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
