<template>
  <el-form ref="postForm" :model="postForm" :rules="rules">
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
          <el-form-item prop="title">
            <MDinput v-model="postForm.title" :maxlength="100" name="name" required>
              书名
            </MDinput>
          </el-form-item>
          <el-row>
            <el-col :span="12">
              <el-form-item label="作者：" :label-width="labelWidth" prop="author">
                <el-input
                  v-model="postForm.author"
                  placeholder="作者"
                />
              </el-form-item>
            </el-col>
            <el-col :span="12">
              <el-form-item label="出版社：" :label-width="labelWidth" prop="publisher">
                <el-input
                  v-model="postForm.publisher"
                  placeholder="出版社"
                />
              </el-form-item>
            </el-col>
          </el-row>
          <el-row>
            <el-col :span="12">
              <el-form-item label="语言：" :label-width="labelWidth" prop="language">
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
import { createBook, getBook } from '../../../api/book'
const defaultForm = {
  title: '',
  author: '',
  publisher: '',
  language: '',
  cover: '',
  rootFile: '',
  url: '',
  originalName: '',
  contents: '',
  filename: '',
  filePath: '',
  coverPath: '',
  unzipPath: '',
  contentsTree: ''
}
const fields = {
  title: '标题',
  author: '作者',
  publisher: '出版社',
  language: '语言'

}
export default {
  components: { Sticky, Warning, EbookUpload, MDinput },
  props: {
    isEdit: Boolean
  },
  data() {
    const validateRequire = (rule, value, callback) => {
      if (value.length === 0) {
        callback(new Error(fields[rule.field] + '必须填写'))
      } else {
        callback()
      }
    }
    return {
      loading: false,
      postForm: {
        title: '',
        author: '',
        publisher: '',
        language: ''
      },
      fileList: [],
      labelWidth: '120px',
      contentsTree: [],
      rules: {
        title: [{ validator: validateRequire }],
        author: [{ validator: validateRequire }],
        publisher: [{ validator: validateRequire }],
        language: [{ validator: validateRequire }]
      }
    }
  },
  created() {
    if (this.isEdit) {
      const filename = this.$route.params.filename
      this.getBookData(filename)
    }
  },
  methods: {
    showGuide() {
    },
    getBookData(filename) {
      getBook({ filename }).then(res => {
        this.setData(res.data)
      })
    },
    onContentClick(data) {
      console.log(data)
      if (data.text) {
        window.open(data.text)
      }
    },
    // 重置表单
    setDefault() {
      this.postForm = Object.assign({}, defaultForm)
      this.contentsTree = []
      this.fileList = []
      this.$refs.postForm.resetFields()
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
      this.setData(data)
    },
    onUploadRemove() {
      this.setDefault()
    },
    submitForm() {
      if (!this.loading) {
        this.loading = true
        console.log(this.$refs.postForm.validate)
        this.$refs.postForm.validate((valid, fields) => {
          console.log(valid, fields)
          if (valid) {
            const book = Object.assign({}, this.postForm)
            delete book.contentsTree
            if (!this.isEdit) {
              createBook(book).then(res => {
                this.$notify.success(res.msg)
                this.loading = false
                this.setDefault()
              }).catch(() => {
                this.loading = false
              })
            } else {

            }
          } else {
            const message = fields[Object.keys(fields)[0]][0].message
            this.$message.error(message)
            this.loading = false
          }
        })
      }
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
