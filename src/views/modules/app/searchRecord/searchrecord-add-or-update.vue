<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="key">
      <el-input v-model="dataForm.key" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="countryId">
      <el-input v-model="dataForm.countryId" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="num">
      <el-input v-model="dataForm.num" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="record">
      <el-input v-model="dataForm.record" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder=""></el-input>
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
          id: 0,
          key: '',
          countryId: '',
          num: '',
          record: '',
          createTime: ''
        },
        dataRule: {
          key: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          countryId: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          num: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          record: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/searchrecord/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.key = data.searchRecord.key
                this.dataForm.countryId = data.searchRecord.countryId
                this.dataForm.num = data.searchRecord.num
                this.dataForm.record = data.searchRecord.record
                this.dataForm.createTime = data.searchRecord.createTime
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
              url: this.$http.adornUrl(`/generator/searchrecord/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'key': this.dataForm.key,
                'countryId': this.dataForm.countryId,
                'num': this.dataForm.num,
                'record': this.dataForm.record,
                'createTime': this.dataForm.createTime
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
