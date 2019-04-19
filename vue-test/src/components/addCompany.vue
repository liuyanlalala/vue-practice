<template>
  <div class="add-company">
    <el-row type="flex" justify="flex-start">
      <el-col :span="12">
        <el-input v-model="searchName" placeholder="请输入企业名称"></el-input>
      </el-col>
      <el-col :span="2" style="margin-left: 10px;">
        <el-button type="primary" @click="handleSearchBtn">搜索</el-button>
      </el-col>
    </el-row>
    <el-row>
      <el-col>
        <el-table
          :data="tableList"
          style="width: 100%"
          stripe
          v-loading="isLoading"
        >
          <el-table-column
            prop="gs_id"
            label="序号"
            width=""
          ></el-table-column>
          <el-table-column
            prop="gsmc"
            label="企业名称"
            width=""
          ></el-table-column>
          <el-table-column
            prop="fr"
            label="法人"
            width=""
          ></el-table-column>
          <el-table-column
            prop="area"
            label="所属地区"
            width=""
          ></el-table-column>
          <el-table-column
            label="操作"
            width=""
          >
            <template slot-scope="scope">
              <el-button v-if="qyType === 1 ? scope.row.know === 2 : scope.row.bad === 2" @click="handleClick(scope.row)" type="normal" size="small">选择
              </el-button>
              <el-button v-if="qyType === 1 ? scope.row.know === 1 : scope.row.bad === 1" type="normal" disabled size="small">已添加</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-col>
    </el-row>
    <el-row type="flex" justify="end">
      <div style="margin-top: 10px;">
        <el-pagination
          background
          :current-page.sync="currentPage"
          layout="total, prev, pager, next"
          :total="total"
          @current-change="handleChangePage"
        ></el-pagination>
      </div>
    </el-row>
    <el-dialog :title="currentCompany.gsmc" :visible.sync="isShowAdd" width="40%" append-to-body>
      <choice-know-people
        @closeModal="closeChoiceModal"
        @refreshList="handleRequestList"
        :qymc="currentCompany.gsmc"
        :gsId="currentCompany.gs_id"
      ></choice-know-people>
    </el-dialog>
  </div>
</template>

<script>
  import host from '../common/host';
  import qs from 'qs';
  import ChoiceKnowPeople from './choiceKnowPeople';

  export default {
    name: "addCompany",
    components: {
      ChoiceKnowPeople
    },
    props: ['qyType'],
    data() {
      return {
        searchName: '',
        tableList: [],
        total: 0,
        isShowAdd: false,
        currentCompany: {},
        currentPage: 1,
        isLoading: true
      }
    },
    methods: {
      handleClick(row) { // 点击选择按钮 弹出选择认识公司人员框
        if (this.qyType === 1) {
          this.currentCompany = row;
          this.isShowAdd = true;
        } else {
          // 调用添加接口 不良企业
          let that = this;
          let params = {
            user_id: 183,
            qy_type: 2,
            qymc: row.gsmc,
            gs_id: row.gs_id
          };

          // 添加不良企业
          this.$axios.post(
            host + '/pc/Repository/saveRepFirm',
            qs.stringify(params),
            {headers: {'Content-Type': 'application/x-www-form-urlencoded'}}
          ).then((res) => {
            if (res.data.status === 'success') {
              this.$message({
                message: '添加成功',
                type: 'success',
                duration: 1500,
                center: true
              });

              // 查询公司列表
              this.handleRequestList();
              this.$emit('handleAddCompanyList');
              setTimeout(function() {
                that.$emit('closeBadModal');
              }, 1000)
            }
          }).catch((error) => {
            console.log(error, 'error')
          })

        }
      },
      closeChoiceModal: function () { // 关闭选择认识公司人员框
        this.isShowAdd = false;
      },
      handleRequestList: function (qymc, page) { // 请求数据列表
        this.isLoading = true;
        let that = this;
        let params = {
          user_id: 183,
          page: page || 1,
          gsmc: qymc || '',
          cate: 1
        };

        // 查询公司列表
        this.$axios.post(
          host + '/index/element_set/getAllCompany',
          qs.stringify(params),
          {headers: {'Content-Type': 'application/x-www-form-urlencoded'}}
        ).then((res) => {
          if (res.data.status === 'success') {
            this.tableList = res.data.rows;
            this.total = res.data.total;
          }
          setTimeout(function () {
            that.isLoading = false;
          }, 500)
        }).catch((error) => {
          console.log(error, 'error')
          that.isLoading = false;
        });

        this.$emit('handleAddCompanyList');
      },
      handleSearchBtn: function () { // 点击搜索按钮
        this.handleRequestList(this.searchName);
      },
      handleChangePage: function (val) { // 改变列表页码
        this.handleRequestList('', val)
      }
    },
    created: function () {
      this.handleRequestList();
    }
  }
</script>

<style scoped>
  .add-company {
    width: 100%;
  }

  .input-search-name {
    width: 80%;
  }

  .search-btn {
    width: 20%;
  }
</style>
