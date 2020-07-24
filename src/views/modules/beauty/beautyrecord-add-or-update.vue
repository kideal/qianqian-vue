<template>
  <el-dialog
    title="添加记录"
    :close-on-click-modal="false"
    @close="closeHandle"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()"
             label-width="80px">

      <el-form-item label="类型" prop="name">
        <el-input v-model="dataForm.name" placeholder=""></el-input>
      </el-form-item>
      <el-form-item label="描述" prop="description">
        <el-input v-model="dataForm.description" placeholder="描述"></el-input>
      </el-form-item>
      <el-form-item label="图片" prop="image">
        <el-upload
          drag
          :action="url_"
          :before-upload="beforeUploadHandle"
          :on-success="successHandle"
          :multiple="true"
          :file-list="files"
          list-type="picture"
          style="text-align: center;">
          <i class="el-icon-upload"></i>
          <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
          <div class="el-upload__tip" slot="tip">只支持jpg、png、gif格式的图片！</div>
        </el-upload>
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
          recordId: 0,
          beautyId: '',
          name: '',
          description: '',
          image: ''
        },
        dataRule: {
          name: [
            {required: true, message: '不能为空', trigger: 'blur'}
          ]
        },
        num: 0,
        successNum: 0,
        url_: '',
        files: []
      }
    },
    methods: {
      init (id) {
        this.dataForm.recordId = 0
        this.visible = true
        this.dataForm.beautyId = id
        this.url_ = this.$http.adornUrl(`/sys/oss/ftpUpload?token=${this.$cookie.get('token')}`)
        this.visible = true
        this.files = []
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/beautyrecord/save`),
              method: 'post',
              data: this.$http.adornData({
                'recordId': this.dataForm.recordId || undefined,
                'beautyId': this.dataForm.beautyId || undefined,
                'name': this.dataForm.name,
                'description': this.dataForm.description,
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
      },

      //上传图片
      // 上传之前
      beforeUploadHandle (file) {
        if (file.type !== 'image/jpg' && file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
          this.$message.error('只支持jpg、png、gif格式的图片！')
          return false
        }
        this.num++
      },
      // 上传成功
      successHandle (response, file, files) {
        this.files = files
        this.successNum++
        if (response && response.code === 0) {

          if (this.num === this.successNum) {
            this.files.forEach((item) => {
            this.dataForm.image=this.dataForm.image+item.response.url+';'
            })
           console.log(this.dataForm.image)
            this.$confirm('操作成功, 是否继续操作?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).catch(() => {
              this.visible = false
            })
          }
        } else {
          this.$message.error(response.msg)
        }
      },

      // 弹窗关闭时
      closeHandle () {
        this.fileList = []
        this.$emit('refreshDataList')
      }
    }
  }
</script>
