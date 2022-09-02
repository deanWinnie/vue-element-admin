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
        <el-col :span="24">
          <ebook-upload
            :file-list="fileList"
            :disabled="isEdit"
            @onSuccess="onUploadSuccess"
            @onRemove="onUploadRemove"
          />
        </el-col>
        <el-col :span="24">
          <el-form-item>
            <MDinput v-model="postForm.title" :maxlength="100" name="name" required>
              书名
            </MDinput>
          </el-form-item>
          <el-row>
            <el-col :span="12">
              <el-form-item label="作者：" :label-width="labelWidth">
                <el-input
                  v-model="postForm.author"
                  placeholder="作者"
                />
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="出版社：" :label-width="labelWidth">
                <el-input
                  v-model="postForm.publisher"
                  placeholder="出版社"
                />
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="12">
              <el-form-item label="语言：" :label-width="labelWidth">
                <el-input
                  v-model="postForm.language"
                  placeholder="语言"
                />
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="根文件：" :label-width="labelWidth">
                <el-input
                  v-model="postForm.rootFile"
                  placeholder="根文件"
                  disabled
                />
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="12">
              <el-form-item label="文件路径：" :label-width="labelWidth">
                <el-input
                  v-model="postForm.filePath"
                  placeholder="文件路径"
                  disabled
                />
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="解压路径：" :label-width="labelWidth">
                <el-input
                  v-model="postForm.unzipPath"
                  placeholder="解压路径"
                  disabled
                />
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="12">
              <el-form-item label="封面路径：" :label-width="labelWidth">
                <el-input
                  v-model="postForm.coverPath"
                  placeholder="封面路径"
                  disabled
                />
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="文件名称：" :label-width="labelWidth">
                <el-input
                  v-model="postForm.originalName"
                  placeholder="文件名称"
                  disabled
                />
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="24">
              <el-form-item label="封面：" :label-width="labelWidth">
                <a v-if="postForm.cover" :href="postForm.cover" target="_blank">
                  <img :src="postForm.cover" alt="" class="preview-img">
                </a>
                <span v-else>无</span>
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="24">
              <el-form-item label="目录：" :label-width="labelWidth">
                <div v-if="postForm.contents && postForm.contents.length>0" class="content-wrapper">
                  <el-tree :data="contentsTree" @node-click="onContentClick" />
                </div>
                <span v-else>无</span>
              </el-form-item>
            </el-col>
          </el-row>
        </el-col>
      </el-row>
    </div>
  </el-form>
</template>
<script>
import Sticky from '@/components/Sticky/index.vue'// 提示组件
import Warning from './Warning.vue'// 吸顶的组件
import EbookUpload from '../../../components/EbookUpload'
import MDinput from '@/components/MDinput/index.vue'
export default {
  components: { Sticky, Warning, EbookUpload, MDinput },
  props: {
    isEdit: Boolean
  },
  data() {
    return {
      loading: false,
      postForm: {},
      fileList: [],
      labelWidth: '120px',
      contentsTree: []
    }
  },
  methods: {
    showGuide() {
    },
    onContentClick(data) {
      console.log(data)
      if (data.text) {
        window.open(data.text)
      }
    },
    setData(data) {
      const {
        title,
        author,
        publisher,
        language,
        cover,
        rootFile,
        url,
        originalName,
        contents,
        filename,
        filePath,
        coverPath,
        unzipPath,
        contentsTree
      } = data
      this.postForm = {
        ...this.postForm,
        title,
        author,
        publisher,
        language,
        cover,
        rootFile,
        url,
        originalName,
        contents,
        filename,
        coverPath,
        unzipPath,
        filePath,
        contentsTree
      }
      this.contentsTree = contentsTree
    },
    onUploadSuccess(data) {
      console.log('onUploadSuccess', data)
      this.setData(data)
    },
    onUploadRemove() {
      console.log('onUploadRemove')
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
    .preview-img{
      width: 200px;
      height: 270px;
    }
  }
</style>
