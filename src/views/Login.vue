<template>
  <div class="login">
    <div class="wrap">
      <h1>A simple s3 management console</h1>
      <div class="login-form">
        <el-tabs v-model="tabs">
          <el-tab-pane label="BaishanCloud"
                       name="baishan">
            <el-form :model="baishanKeys"
                     status-icon
                     :rules="baishanRules"
                     ref="baishanKeys"
                     label-position="left"
                     label-width="80px"
                     class="demo-ruleForm">
              <el-form-item label="accesskey"
                            prop="accesskey">
                <el-input type="text"
                          v-model="baishanKeys.accesskey"
                          autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="secretkey"
                            prop="secretkey">
                <el-input type="password"
                          v-model="baishanKeys.secretkey"
                          autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary"
                           @click="submitForm('baishanKeys')">Submit</el-button>
                <el-button @click="resetForm('baishanKeys')">Reset</el-button>
              </el-form-item>
            </el-form>
          </el-tab-pane>
          <el-tab-pane label="AWS S3"
                       name="aws">
            <el-form :model="awsKeys"
                     status-icon
                     :rules="awsRules"
                     ref="awsKeys"
                     label-position="left"
                     label-width="80px"
                     class="demo-ruleForm">
              <el-form-item label="accesskey"
                            prop="accesskey">
                <el-input type="text"
                          v-model="awsKeys.accesskey"
                          autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="secretkey"
                            prop="secretkey">
                <el-input type="password"
                          v-model="awsKeys.secretkey"
                          autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary"
                           @click="submitForm('awsKeys')">Submit</el-button>
                <el-button @click="resetForm('awsKeys')">Reset</el-button>
              </el-form-item>
            </el-form>
          </el-tab-pane>
          <el-tab-pane label="Others"
                       name="others">
            <el-form :model="othersKeys"
                     status-icon
                     :rules="othersRules"
                     ref="othersKeys"
                     label-position="left"
                     label-width="80px"
                     class="demo-ruleForm">
              <el-form-item label="accesskey"
                            prop="accesskey">
                <el-input type="text"
                          v-model="othersKeys.accesskey"
                          autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="secretkey"
                            prop="secretkey">
                <el-input type="password"
                          v-model="othersKeys.secretkey"
                          autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="host"
                            prop="host">
                <el-input type="text"
                          v-model="othersKeys.host"
                          autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary"
                           @click="submitForm('othersKeys')">Submit</el-button>
                <el-button @click="resetForm('othersKeys')">Reset</el-button>
              </el-form-item>
            </el-form>
          </el-tab-pane>
        </el-tabs>
      </div>
    </div>
  </div>
</template>
<script>
import { login } from '@/service/aws-http'
export default {
  data() {
    return {
      baishanKeys: {
        accesskey: '',
        secretkey: '',
        host: 'http://ss.bscstorage.com',
      },
      awsKeys: {
        accesskey: '',
        secretkey: '',
        host: 'http://s3.us-west-1.amazonaws.com',
      },
      othersKeys: {
        accesskey: '',
        secretkey: '',
        host: '',
      },
      baishanRules: {
        accesskey: [{ required: true, trigger: 'blur' }],
        secretkey: [{ required: true, trigger: 'blur' }],
      },
      awsRules: {
        accesskey: [{ required: true, trigger: 'blur' }],
        secretkey: [{ required: true, trigger: 'blur' }],
      },
      othersRules: {
        accesskey: [{ required: true, trigger: 'blur' }],
        secretkey: [{ required: true, trigger: 'blur' }],
        host: [{ required: true, trigger: 'blur' }],
      },
      tabs: 'baishan',
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.setKeysAndGetBuckets(formName)
        } else {
          this.$notify.error('error submit!!')
          return false
        }
      })
    },
    async setKeysAndGetBuckets(formName) {
      const keys =
        formName === 'awsKeys'
          ? this.awsKeys
          : formName === 'baishanKeys'
            ? this.baishanKeys
            : this.othersKeys
      await this.$store.dispatch('setValueWithStorage', { keys })

      const res = await login(keys)
      if (Object.keys(res).length > 0) {
        await this.$store.dispatch('setValues', res)
        this.$router.push({ name: 'bucket' })
      }
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    },
  },
}
</script>
<style scoped lang="less">
.wrap {
  width: 400px;
  padding: 20px;
  margin: 160px auto 10px;
}
h1 {
  padding-bottom: 30px;
}
</style>
