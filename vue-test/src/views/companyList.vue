<template>
  <div class="company-wrapper">
    <el-row>
      <el-col :span="24">
        <div class="grid-content company-title">资源库</div>
      </el-col>
    </el-row>
    <el-row style="margin: 20px 0 30px 0;">
      <el-col :span="16" :offset="4">
        <el-row :span="24">
          <el-col :span="12">
            <div :class="clickNum === 1 ? 'type-common type-left' : 'type-common type-right'" @click="handleExChange(1)">我认识/合作的企业</div>
          </el-col>
          <el-col :span="12">
            <div :class="clickNum === 2 ? 'type-common type-left' : 'type-common type-right'" @click="handleExChange(2)">我登记的不良企业</div>
          </el-col>
        </el-row>
      </el-col>
    </el-row>
    <div :class="clickNum === 1 ? 'good-company' : 'bad-company'" >
      <el-row type="flex" justify="space-between">
        <el-col :xs="12" :sm="12" :md="8" :lg="6">
          <div class="table-name-common my-company">
            <h3>我认识/合作的企业</h3>
            <el-button type="primary" style="margin-left: 10px;" @click="isShowAdd=true">添加</el-button>
            <el-dialog title="添加企业" :visible.sync="isShowAdd" width="80%">
              <add-company
                :qyType="1"
                @handleAddCompanyList="handleRequestCompany"
              ></add-company>
            </el-dialog>
          </div>
        </el-col>
        <el-col :md="8" :lg="6" class="hidden-sm-and-down">
          <div class="table-name-common my-company-operate">
            <el-input style="padding-right: 20px;" v-model="companyName" placeholder="请输入企业名称"></el-input>
            <el-button type="primary" @click="handleSearchBtn">搜索</el-button>
            <el-button type="primary" @click="handleResetBtn">重置</el-button>
          </div>
        </el-col>
      </el-row>
      <el-row type="flex" justify="center">
        <el-col :span="23">
          <div class="table-wrapper">
            <el-table
              :data="tableList"
              style="width: 100%"
              stripe
              v-loading="isLoading"
              :cell-style="showCellStyle"
              :header-cell-style="showCellStyle"
            >
              <el-table-column
                prop="sort"
                label="#"
                width="100px"
              ></el-table-column>
              <el-table-column
                label="企业名称"
                width=""
              >
                <template slot-scope="scope">
                  <span style="color: #409eff;">{{scope.row.qymc}}</span>
                </template>
              </el-table-column>
              <el-table-column
                prop="duty"
                label="我认识(公司负责人/业务负责人/业务人员)"
                width=""
              ></el-table-column>
              <el-table-column
                prop="remark"
                label="备注"
                width=""
              ></el-table-column>
              <el-table-column
                label="操作"
                width=""
              >
                <template slot-scope="scope">
                  <el-button @click="handleClick(scope.row)" type="normal" size="small">编辑</el-button>
                  <el-button type="danger" size="small" @click="handleDeleteBtn(scope.row, 1)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-col>
      </el-row>
      <el-row type="flex" justify="center">
        <el-col :span="23">
          <div class="page-wrapper" v-show="clickNum === 1">
            <el-pagination
              background
              :current-page.sync="currentPage"
              layout="total, prev, pager, next"
              :total="total"
              @current-change="handleChangePage"
            ></el-pagination>
          </div>
        </el-col>
      </el-row>
      <div v-if="isShowEdit">
        <el-dialog :title="currentCompany.qymc" :visible.sync="isShowEdit" width="40%">
          <edit-company
            @closeModal="closeChoiceModal"
            @refreshList="handleRequestCompany"
            :qymc="currentCompany.qymc"
            :gsId="currentCompany.gs_id"
            :id="currentCompany.id"
            :duty="currentCompany.duty ? currentCompany.duty.split(',') : []"
            :remark="currentCompany.remark"
          ></edit-company>
        </el-dialog>
      </div>
    </div>
    <div :class="clickNum === 2 ? 'good-company' : 'bad-company'" >
      <el-row type="flex" justify="space-between">
        <el-col :xs="12" :sm="12" :md="8" :lg="6">
          <div class="table-name-common my-company">
            <h3>我登记的不良企业</h3>
            <el-button type="primary" style="margin-left: 10px;" @click="isShowAddBad=true">添加</el-button>
            <el-dialog title="添加企业" :visible.sync="isShowAddBad" width="80%">
              <add-company
                :qyType="2"
                @closeBadModal="isShowAddBad=false"
                @handleAddCompanyList="handleRequestCompanyBad"
              ></add-company>
            </el-dialog>
          </div>
        </el-col>
        <el-col :md="8" :lg="6" class="hidden-sm-and-down">
          <div class="table-name-common my-company-operate">
            <el-input style="padding-right: 20px;" v-model="companyNameBad" placeholder="请输入企业名称"></el-input>
            <el-button type="primary" @click="handleSearchBtnBad">搜索</el-button>
            <el-button type="primary" @click="handleResetBtnBad">重置</el-button>
          </div>
        </el-col>
      </el-row>
      <el-row type="flex" justify="center">
        <el-col :span="23">
          <div class="table-wrapper">
            <el-table
              :data="tableListBad"
              style="width: 100%"
              stripe
              v-loading="isLoadingBad"
              :cell-style="showCellStyle"
              :header-cell-style="showCellStyle"
            >
              <el-table-column
                prop="sort"
                label="#"
                width="100px"
              ></el-table-column>
              <el-table-column

                label="企业名称"
                width=""
              >
                <template slot-scope="scope">
                  <span style="color: #409eff;">{{scope.row.qymc}}</span>
                </template>
              </el-table-column>
              <el-table-column
                prop="count_bad"
                label="不良记录"
                width=""
              ></el-table-column>
              <el-table-column
                label="操作"
                width=""
              >
                <template slot-scope="scope">
                  <el-button @click="handleClickBad(scope.row)" type="normal" size="small">编辑</el-button>
                  <el-button type="danger" size="small" @click="handleDeleteBtn(scope.row, 2)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-col>
      </el-row>
      <el-row type="flex" justify="center">
        <el-col :span="23">
          <div class="page-wrapper" v-show="clickNum === 2">
            <el-pagination
              background
              :current-page.sync="currentPageBad"
              layout="total, prev, pager, next"
              :total="totalBad"
              @current-change="handleChangePageBad"
            ></el-pagination>
          </div>
        </el-col>
      </el-row>
      <div v-if="isShowEdit">
        <el-dialog :title="currentCompany.qymc" :visible.sync="isShowEdit" width="40%">
          <edit-company
            @closeModal="closeChoiceModal"
            @refreshList="handleRequestCompany"
            :qymc="currentCompany.qymc"
            :gsId="currentCompany.gs_id"
            :id="currentCompany.id"
            :duty="currentCompany.duty ? currentCompany.duty.split(',') : []"
            :remark="currentCompany.remark"
          ></edit-company>
        </el-dialog>
      </div>
    </div>
  </div>
</template>

<script>
  import host from '../common/host';
  import qs from 'qs';
  import AddCompany from '../components/addCompany';
  import EditCompany from '../components/editCompany';

  export default {
    name: "companyList",
    components: {
      AddCompany,
      EditCompany
    },
    data() {
      return {
        companyName: '',
        companyNameBad: '',
        tableList: [], // 合作企业
        isShowAdd: false, // 添加合作企业弹窗的显示
        isShowEdit: false, // 编辑合作企业弹的显示
        currentCompany: {},
        currentPage: 1,
        total: 0, // 合作企业
        tableListBad: [], // 不良企业
        totalBad: 0, // 不良企业
        currentPageBad: 1,
        isShowAddBad: false,
        isLoading: true,
        clickNum: 1,
        isLoadingBad: true
      }
    },
    created: function () {
      // 我合作的企业
      this.handleRequestCompany();

      // 我登记的不良企业
      this.handleRequestCompanyBad();

      // bad字段
    },
    methods: {
      handleExChange: function (num) { // 点击切换合作企业/不良企业
        this.clickNum = num;
      },
      handleClick(row) { // 点击编辑按钮
        this.isShowEdit = true;
        this.currentCompany = row;
      },
      handleClickBad(row) { // 点击编辑按钮 不良企业
        // this.isShowEdit = true;
        // this.currentCompany = row;
      },
      handleAddCompany: function() {
        this.isShowAdd = true;
      },
      closeChoiceModal: function () { // 关闭编辑弹窗
        this.isShowEdit = false;
      },
      handleRequestCompany: function (name, page) { // 查询企业列表 合作企业
        this.isLoading = true;
        let that = this;
        let params = {
          user_id: 183,
          qymc: name || '',
          qy_type: 1,
          page: page || 1
        };
        this.$axios.post(
          host + '/pc/Repository/searchRepFirm',
          qs.stringify(params),
          {headers: {'Content-Type': 'application/x-www-form-urlencoded'}}
        ).then((res) => {

          if (res.data.status === 'success') {
            this.tableList = res.data.rows.map((item, index) => {
              return {
                qymc: item.qymc,
                remark: item.remark,
                duty: item.duty,
                sort: page ? (page - 1) * 10 + index + 1 : index + 1,
                gs_id: item.gs_id,
                id: item.id
              }
            });
            this.total = res.data.total;
          }
          setTimeout(function () {
            that.isLoading = false;
          }, 500)
        }).catch((error) => {
          this.isLoading = false;
          console.log(error, 'error')
        })
      },
      handleRequestCompanyBad: function (name, page) { // 查询企业列表 不良企业
        this.isLoadingBad = true;
        let that = this;
        let params = {
          user_id: 183,
          qymc: name || '',
          qy_type: 2,
          page: page || 1
        };
        this.$axios.post(
          host + '/pc/Repository/searchRepFirm',
          qs.stringify(params),
          {headers: {'Content-Type': 'application/x-www-form-urlencoded'}}
        ).then((res) => {

          if (res.data.status === 'success') {
            this.tableListBad = res.data.rows.map((item, index) => {
              return {
                qymc: item.qymc,
                sort: page ? (page - 1) * 10 + index + 1 : index + 1,
                gs_id: item.gs_id,
                id: item.id,
                count_bad: item.count_bad
              }
            });
            this.totalBad = res.data.total;
          }
          setTimeout(function () {
            that.isLoadingBad = false;
          }, 500)
        }).catch((error) => {
          this.isLoadingBad = false;
          console.log(error, 'error')
        })
      },
      handleSearchBtn: function () { // 点击搜索按钮
        this.handleRequestCompany(this.companyName);
      },
      handleSearchBtnBad: function () { // 点击搜索按钮
        this.handleRequestCompanyBad(this.companyNameBad);
      },
      handleChangePage: function (val) { // 改变列表页码
        this.currentPage = val;
        this.handleRequestCompany('', val)
      },
      handleChangePageBad: function (val) { // 改变列表页码
        this.currentPageBad = val;
        this.handleRequestCompanyBad('', val)
      },
      handleResetBtn: function () { // 点击重置按钮
        this.companyName = '';
        this.handleRequestCompany('', this.currentPage);
      },
      handleResetBtnBad: function () { // 点击重置按钮
        this.companyNameBad = '';
        this.handleRequestCompanyBad('', this.currentPageBad);
      },
      handleDeleteBtn: function (row, type) { // 点击删除按钮
        this.$confirm('此操作将删除该数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => { // 点击确定
          // 调用删除
          let params = {
            rep_id: row.id,
            bad_id: '',
            qy_type: type
          };
          this.$axios.post(
            host + '/pc/Repository/delRepFirm',
            qs.stringify(params),
            {headers: {'Content-Type': 'application/x-www-form-urlencoded'}}
          ).then((res) => {
            if (res.data.status === 'success') {
              if (type === 1) {
                this.handleRequestCompany('', this.currentPage);
              } else {
                this.handleRequestCompanyBad('', this.currentPageBad);
              }
              this.$message({
                type: 'success',
                message: '删除成功!'
              });
            }
          }).catch((error) => {
            console.log(error, 'error')
          });

        }).catch(() => { // 点击取消
          // this.$message({
          //   type: 'info',
          //   message: '已取消删除'
          // });
        });

      },
      showCellStyle({row, column, rowIndex, columnIndex}) { // 设置table单元格样式
        if (columnIndex !== 1) {
          return 'text-align: center'
        }
      }

    }
  }
</script>

<style scoped>
  .company-wrapper {
    width: 100%;
    height: 100%;
    overflow: hidden;
  }

  .company-title {
    width: 100%;
    background-color: #324157;
    color: #fff;
    font-size: 32px;
    text-align: center;
    padding: 14px 0;
  }

  .good-company {
    height: auto;
    transition: all .6s ease;
  }

  .bad-company {
    height: 0;
    overflow: hidden;
    transition: all .6s ease;
  }

  .type-common {
    text-align: center;
    font-size: 18px;
    height: 55px;
    line-height: 55px;
  }

  .type-left {
    background-color: #66b1ff;
    color: #fff;
  }

  .type-right {
    background-color: #f2f2f2;
    color: #333;
  }

  .table-name-common {
    width: 100%;
    display: flex;
    justify-content: flex-start;
    align-items: center;
    font-size: 14px;
    padding: 0 20px;
  }

  .my-company h3 {
    font-size: 18px;
  }

  .page-wrapper {
    width: 100%;
    display: flex;
    justify-content: flex-end;
    margin-top: 10px;
    position: fixed;
    bottom: 30px;
    right: 30px;
  }
</style>
