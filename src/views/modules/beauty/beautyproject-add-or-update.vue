<template>
  <el-dialog
    :title="!dataForm.beautyId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">

    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="80px">

      <el-form-item label="姓名" prop="vipUserId">
        <el-select v-model="dataForm.vipUserId" filterable placeholder="请选择">
          <el-option
            v-for="item in vip_user_options"
            :key="item.value"
            :label="item.name"
            :wechatCode="item.wechatCode"
            :mobile="item.mobile"
            :value="item.value">
            <span>{{ item.label }}</span>
            <span>{{ item.wechatCode }}</span>
            <span>{{ item.mobile }}</span>
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="内容" prop="name">
        <el-input
          type="textarea"
          :rows="2"
          placeholder="请输入内容"
          v-model="dataForm.name">
        </el-input>
      </el-form-item>
      <el-form-item label="时间" prop="date">
        <el-date-picker
          v-model="dataForm.date"
          value-format="yyyy-MM-dd-hh-mm-ss"
          type="date"
          placeholder="选择日期">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="地址" prop="location">
        <el-input v-model="dataForm.location" placeholder="地址"></el-input>
      </el-form-item>
      <el-form-item label="金额" prop="amount">
        <el-input v-model="dataForm.amount" placeholder="金额"></el-input>
      </el-form-item>
      <el-form-item label="美容医师" prop="operator">
        <el-select v-model="dataForm.operator" filterable placeholder="请选择">
          <el-option
            v-for="item in operator_options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>

    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        vip_user_data: [],
        visible: false,
        dataForm: {
          beautyId: 0,
          vipUserId: '',
          name: '',
          date: '',
          location: '',
          amount: '',
          operator: ''
        },
        dataRule: {
          vipUserId: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          name: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ],
          date: [
            {required: true, message: '时间不能为空', trigger: 'blur'}
          ],
          location: [
            {required: true, message: '地址不能为空', trigger: 'blur'}
          ],
          amount: [
            {required: true, message: '金额不能为空', trigger: 'blur'}
          ],
          operator: [
            {required: true, message: '美容医师不能为空', trigger: 'blur'}
          ]
        },
        vip_user_options: [],
        operator_options: [{
          value: '谢医师',
          label: '谢医师'
        }],
        value: '马化腾'
      }
    },
    methods: {
      init (id) {
        this.dataForm.beautyId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.beautyId) {
            this.$http({
              url: this.$http.adornUrl(`/beautyproject/info/${this.dataForm.beautyId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.vipUserId = data.beautyProject.vipUserId
                this.dataForm.name = data.beautyProject.name
                this.dataForm.date = data.beautyProject.date
                this.dataForm.location = data.beautyProject.location
                this.dataForm.amount = data.beautyProject.amount
                this.dataForm.operator = data.beautyProject.operator
              }
            })
          }
        })
        // 获取options
        this.$http({
          url: this.$http.adornUrl(`/vipuser/list`),
          method: 'get',
          params: this.$http.adornParams({})
        }).then(({data}) => {
          this.vip_user_options = []
          data.page.list.forEach(element => {
            this.vip_user_options.push({
              value: element.userId,
              label: element.userName,
              wechatCode: element.wechatCode,
              mobile: element.mobile
            })
          })
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/beautyproject/${!this.dataForm.beautyId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'beautyId': this.dataForm.beautyId || undefined,
                'vipUserId': this.dataForm.vipUserId,
                'name': this.dataForm.name,
                'date': this.dataForm.date,
                'location': this.dataForm.location,
                'amount': this.dataForm.amount,
                'operator': this.dataForm.operator
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
