<template>
  <el-form ref="postForm" :model="postForm">
    <Sticky :class-name="'sub-navbar'">
      <el-button v-if="!isEdit" @click="showGuide">显示帮助</el-button>
      <el-button
        v-loading="loading"
        type="success"
        style="margin-left:10px;"
        @click="submitForm"
      >
        {{ isEdit?'编辑电子书':'新增电子书' }}
      </el-button>
    </Sticky>
    <div class="detail-container">
      <el-row>
        <Warning />
        <el-col :span="24" />
        <el-col :span="24" />
      </el-row>
    </div>
  </el-form>
</template>
<script>
import Sticky from '@/components/Sticky/index.vue'// 提示组件
import Warning from './Warning.vue'// 吸顶的组件
export default {
  components: { Sticky, Warning },
  props: {
    isEdit: Boolean
  },
  data() {
    return {
      loading: false,
      postForm: {}
    }
  },
  methods: {
    showGuide() {
    },
    submitForm() {
      this.loading = true
      setTimeout(() => {
        this.loading = false
      }, 3000)
    }
  }
}
</script>
<style scoped lang="scss">
  .detail-container{
    padding: 40px 50px 20px;
  }
</style>
