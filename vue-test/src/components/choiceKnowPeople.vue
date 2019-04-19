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
    name: "choiceKnowPeople",
    props: ['qymc', 'gsId'],
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
        duty: ''
      }
    },
    methods: {
      cancelShow: function () { // 点击取消按钮
        this.$emit('closeModal')
      },
      getSelectedValue: function (value) { // 获取下拉框选中值
        if (value.length > 0) {
          this.duty = value.join(',');
        } else {
          this.duty = '';
        }
      },
      clickSureBtn: function () { // 点击确定按钮
        let that = this;
        let params = {
          user_id: 183,
          qy_type: 1,
          qymc: this.qymc,
          gs_id: this.gsId,
          duty: this.duty
        };

        // 添加合作企业
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
            this.selectValue = [];

            // 查询公司列表
            this.$emit('refreshList');
            setTimeout(function() {
              that.$emit('closeModal');
            }, 1000)
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
