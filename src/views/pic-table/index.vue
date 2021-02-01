<template>
  <div class="app-container">
    <el-row :gutter="20">
      <el-col :span="24">
        <el-button type="primary" @click="handleAdd">上传图片</el-button>
      </el-col>
    </el-row>
    <div v-loading="loading">
      <div class="table-box">
        <el-table :data="table" border>
          <el-table-column label="图片名称" prop="name" />
          <el-table-column label="图片预览">
            <template slot-scope="scope">
              <pic-container :url="scope.row.url" />
            </template>
          </el-table-column>
          <el-table-column label="图片大小" prop="size" sortable />
          <el-table-column label="上传时间" prop="time" sortable  />
          <el-table-column label="操作">
            <template slot-scope="scope">
              <el-button type="primary" @click="handleEdit(scope.row)">修改名称</el-button>
              <el-button type="danger" @click="delFile(scope.row.id)"
                >删除</el-button
              >
            </template>
          </el-table-column>
        </el-table>
      </div>
      <div class="pagintion">
        <el-pagination
          background
          layout="prev, pager, next"
          :total="total"
          :current-page="currentPage"
          :page-size="pageSize"
          @current-change="handleChange"
        />
      </div>
    </div>

    <el-dialog
      :destroy-on-close="true"
      title="上传图片"
      width="500px"
      :visible.sync="dialogShow"
      :close-on-click-modal="false"
      center
    >
      <div class="wrap">
        <el-form ref="fileForm" :model="form" :rules="rules">
          <el-form-item label="图片名称" prop="name" >
            <el-input v-model="form.name" placeholder="图片名称"> </el-input>
          </el-form-item>
          <el-form-item label="图片上传" prop="url">
            <el-upload
              ref="upload"
              :disabled="!!form.url"
              class="upload-demo"
              drag
              action="/api/upload"
              :headers="{ token: token }"
              accept=".jpg,.jpeg,.png"
              :before-upload="beforeUpload"
              :on-success="handleSuccess"
            >
              <i class="el-icon-upload" />
              <div class="el-upload__text">
                将文件拖到此处，或<em>点击上传</em>
              </div>
              <div slot="tip" class="el-upload__tip">
                只能上传jpg/jpeg/png文件，且不超过1mb              </div>
            </el-upload>
          </el-form-item>
        </el-form>
      </div>
      <template slot="footer">
        <el-button type="primary" @click="submit">保存</el-button>
      </template>
    </el-dialog>
  </div>
</template>
<script>
import PicContainer from "@/components/imgContainer";
import { updateFileApi, getFileListApi, deleteFileApi } from "@/api/files";
import { getToken } from "@/utils/auth";
import formValidatorMixins from '@/components/formValidatorMixins';
export default {
  components: { PicContainer },
  mixins:[formValidatorMixins],
  data() {
    return {
      table: [],
      file: {},
      form: {
        id:"",
        name: "",
        size: "",
        url: ""
      },
      total: 0,
      currentPage: 1,
      pageSize: 30,
      dialogShow: false,
      loading: false,
      token: getToken(),
      rules:{
        name:[
          {
            required:true,
            message:'必填!'
          },
          {
            type:'string',
            min:1,
            max:20,
            message:'最长20字符'
          }
        ],
        url:[
          {
            required:true,
            type:'string',
            message:"图片必须上传"
          }
        ]
      }
    };
  },
  created() {
    this.fetchData();
  },
  methods: {
    handleChange(val) {
      this.currentPage = val;
      this.fetchData();
    },
    fetchData() {
      this.loading = true;
      const { currentPage, pageSize } = this;
      getFileListApi({ currentPage, pageSize })
        .then(res => {
          const { docs, totalDocs, limit, page } = res;
          this.total = totalDocs;
          this.table = docs;
          this.pageSize = limit;
          this.currentPage = page;
        })
        .finally(() => {
          this.loading = false;
        });
    },
    handleAdd() {
      this.dialogShow = true;
    },
    handleEdit(val){
      this.form = Object.assign({},val);
      this.dialogShow = true;
    },
    submit() {
      if(typeof this.form.size === 'string'){
        this.form.size = 0
      }
      this.validateForm('fileForm').then(() => {
        updateFileApi(this.form).then(res => {
        this.$message.success("更新成功");
        this.dialogShow = false;
        this.fetchData()
      })
      })
      
    },
    delFile(id) {
      deleteFileApi(id).then(res => {
        this.$message.success("删除成功");
        this.fetchData();
      });
    },
    beforeUpload(file) {
      if (file.size >  1024 * 1024) {
        this.$message.error("图片大小超出限制");
        return false;
      }
      return true;
    },
    handleSuccess(res, file) {
      if (res.code && res.code === 200) {
        this.$message.success("上传成功！");
        const { size, url } = res.data;
        this.form.url = url;
        this.form.size = size;
      } else {
        this.$refs['upload'].clearFiles()
        this.$message.error("上传失败!");
      }
    }
  },
  watch:{
    dialogShow(val){
      if(!val){
        this.form = {
        id:"",
        name: "",
        size: "",
        url: ""
      }
      this.$nextTick(() => {
          this.$refs['fileForm'].clearValidate()
      })
      }
    }
  }
};
</script>
<style lang="scss"></style>
