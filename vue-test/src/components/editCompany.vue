<template>
  <div class="choice-wrapper">
    <el-row type="flex" justify="center">
      <el-col :span="20">
        <span>选择你认识该公司的人员 (备注：可多选)</span>
      </el-col>
    </el-row>
    <el-row type="flex" justify="center">
      <el-col :span="20">
        <div class="select-wrapper">
          <el-select @change="getSelectedValue"  style="width: 100%;" v-model="selectValue" multiple placeholder="请选择">
            <el-option
              v-for="(item, index) in options"
              :key="index"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </div>
      </el-col>
    </el-row>
    <el-row type="flex" justify="center">
      <el-col :span="20">
        <el-row>
          <span>备注:</span>
        </el-row>
        <el-row>
          <div class="select-wrapper">
            <el-input
              type="textarea"
              :autosize="{minRows: 2, maxRows: 4}"
              placeholder="请输入内容"
              v-model="curRemark"
            ></el-input>
          </div>
        </el-row>
      </el-col>
    </el-row>
    <el-row type="flex" justify="center">
      <el-col :span="8">
        <div class="operate-btn">
          <el-button type="normal" @click="cancelShow">取消</el-button>
          <el-button type="primary" @click="clickSureBtn">确定</el-button>
        </div>
      </el-col>

    </el-row>
  </div>
</template>

<script>
  import host from '../common/host';
  import qs from 'qs';
  export default {
    name: "editCompany",
    props: ['qymc', 'gsId', 'id', 'duty', 'remark'],
    data() {
      return {
        selectValue: [],
        options: [
          {
            value: '公司负责人',
            label: '公司负责人'
          }, {
            value: '业务负责人',
            label: '业务负责人'
          }, {
            value: '业务人员',
            label: '业务人员'
          }, {
            value: '其他',
            label: '其他'
          }
        ],
        curRemark: ''
      }
    },
    created: function () {
      this.selectValue = this.duty;
      this.curRemark = this.remark;
    },
    methods: {
      cancelShow: function () { // 点击取消按钮
        this.$emit('closeModal')
      },
      getSelectedValue: function (value) { // 获取下拉框选中值
        // console.log(value, '选中值');
        // if (value.length > 0) {
        //   this.selectValue = value.join(',');
        // } else {
        //   this.selectValue = '';
        // }
      },
      clickSureBtn: function () { // 点击确定按钮
        let that = this;

        let params = {
          rep_id: this.id,
          qy_type: 1,
          duty: this.selectValue.join(','),
          remark: this.curRemark
        };

        // 添加合作企业
        this.$axios.post(
          host + '/pc/Repository/saveRepFirm',
          qs.stringify(params),
          {headers: {'Content-Type': 'application/x-www-form-urlencoded'}}
        ).then((res) => {
          if (res.data.status === 'success') {
            this.$message({
              message: '编辑成功',
              type: 'success',
              duration: 1500,
              center: true
            });

            // 查询公司列表
            this.$emit('refreshList');
            this.$emit('closeModal');
          }
        }).catch((error) => {
          console.log(error, 'error')
        })
      }
    }
  }
</script>

<style scoped>
  .choice-wrapper {
    width: 100%;
  }

  .select-wrapper {
    width: 100%;
    margin: 10px 0 50px 0;
  }

  .operate-btn {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
</style>

