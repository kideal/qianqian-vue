<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="顾客姓名" prop="userName">
      <el-input v-model="dataForm.userName" placeholder="顾客姓名"></el-input>
    </el-form-item>
    <el-form-item label="顾客微信号" prop="wechatCode">
      <el-input v-model="dataForm.wechatCode" placeholder="顾客微信号"></el-input>
    </el-form-item>
    <el-form-item label="顾客QQ号" prop="qqCode">
      <el-input v-model="dataForm.qqCode" placeholder="顾客QQ号"></el-input>
    </el-form-item>
    <el-form-item label="顾客手机号" prop="mobile">
      <el-input v-model="dataForm.mobile" placeholder="顾客手机号"></el-input>
    </el-form-item>
    <el-form-item label="顾客地址" prop="address">
      <el-input v-model="dataForm.address" placeholder="顾客地址"></el-input>
    </el-form-item>
    <el-form-item label="顾客照片" prop="image">
      <el-input v-model="dataForm.image" placeholder="顾客照片"></el-input>
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
        visible: false,
        dataForm: {
          userId: 0,
          userName: '',
          wechatCode: '',
          qqCode: '',
          mobile: '',
          address: '',
          image: '',
        },
        dataRule: {
          userName: [
            { required: true, message: '顾客姓名不能为空', trigger: 'blur' }
          ],
          wechatCode: [
            { required: true, message: '顾客微信号不能为空', trigger: 'blur' }
          ],
          qqCode: [
            { required: true, message: '顾客QQ号不能为空', trigger: 'blur' }
          ],
          mobile: [
            { required: true, message: '顾客手机号不能为空', trigger: 'blur' }
          ],
          address: [
            { required: true, message: '顾客地址不能为空', trigger: 'blur' }
          ],
          image: [
            { required: true, message: '顾客照片不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.userId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.userId) {
            this.$http({
              url: this.$http.adornUrl(`/vipuser/info/${this.dataForm.userId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userName = data.vipUser.userName
                this.dataForm.wechatCode = data.vipUser.wechatCode
                this.dataForm.qqCode = data.vipUser.qqCode
                this.dataForm.mobile = data.vipUser.mobile
                this.dataForm.address = data.vipUser.address
                this.dataForm.image = data.vipUser.image
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/vipuser/${!this.dataForm.userId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'userId': this.dataForm.userId || undefined,
                'userName': this.dataForm.userName,
                'wechatCode': this.dataForm.wechatCode,
                'qqCode': this.dataForm.qqCode,
                'mobile': this.dataForm.mobile,
                'address': this.dataForm.address,
                'image': this.dataForm.image
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
