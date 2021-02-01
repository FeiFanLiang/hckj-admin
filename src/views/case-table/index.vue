<template>
  <div class="app-container">
    <el-row>
      <el-button type="primary" @click="handleAdd">添加案例</el-button>
    </el-row>
    <div class="table-box">
      <el-table :data="tableData" border>
        <el-table-column label="案例标题" prop="title" show-overflow-tooltip />
        <el-table-column
          label="案例简述"
          prop="subTitle"
          show-overflow-tooltip
        />
        <el-table-column label="发布时间" prop="time" />
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button type="primary" @click="handleEdit(scope.row)"
              >编辑</el-button
            >
            <el-button
              type="danger"
              @click="handleDelete(scope.row.id, scope.$index)"
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
    <el-dialog title="案例详情" center width="900px" :visible.sync="dialogShow">
      <el-form :model="form" ref="caseForm" :rules="rules" label-position="top">
        <el-form-item label="案例标题" prop="title">
          <el-input
            v-model="form.title"
            type="textarea"
            placeholder="输入案例标题"
          />
        </el-form-item>
        <el-form-item label="案例首页图片（240X400尺寸）" prop="titlePic">
          <pic-select v-model="form.titlePic" placeholder="案例首页图片" />
        </el-form-item>
        <el-form-item label="首页图片预览">
          <pic-container v-if="form.titlePic" :url="form.titlePic" />
        </el-form-item>
        <el-form-item
          label="首页案例Icon图片（60X60）"
          prop="indexIcon"
        >
          <pic-select
            v-model="form.indexIcon"
            placeholder="首页案例Icon图片"
          />
        </el-form-item>
        <el-form-item label="二级导航栏标题" prop="navTitle">
          <el-input v-model="form.navTitle" placeholder="导航栏二级标题"></el-input>
        </el-form-item>
        <el-form-item label="案例简介(首页展示)" prop="subTitle">
          <el-input
            v-model="form.subTitle"
            type="textarea"
            placeholder="案例简介（段落之间请使用$$分割）"
          />
        </el-form-item>
        <el-form-item label="详情部分-1-标题" prop="paragraphTitle_1">
          <el-input
            v-model="form.paragraphTitle_1"
            type="textarea"
            placeholder="详情部分-1-标题"
          />
        </el-form-item>
        <el-form-item label="详情部分-1-图片（320X300尺寸）" prop="paragraphImg_1">
          <pic-select
            v-model="form.paragraphImg_1"
            placeholder="详情部分-1-图片"
          />
        </el-form-item>
        <el-form-item label="图片预览">
          <pic-container v-if="form.paragraphImg_1" :url="form.paragraphImg_1" />
        </el-form-item>
        <el-form-item label="详情部分-1-内容" prop="paragraphContent_1">
          <el-input
            v-model="form.paragraphContent_1"
            type="textarea"
            placeholder="详情部分-1-内容（段落之间请使用$$分割）"
          />
        </el-form-item>
        <el-form-item label="详情部分-2-标题" prop="paragraphTitle_2">
          <el-input
            v-model="form.paragraphTitle_2"
            type="textarea"
            placeholder="详情-2-标题"
          />
        </el-form-item>
        <el-form-item label="详情部分-2-内容" prop="paragraphContent_2">
          <el-input
            v-model="form.paragraphContent_2"
            type="textarea"
            placeholder="详情-2-内容"
          />
        </el-form-item>
        <el-form-item
          label="详情部分-2-图-1-标题"
          prop="paragraphContent2ImgTitle_1"
        >
          <el-input
            v-model="form.paragraphContent2ImgTitle_1"
            placeholder="详情-2-图-1-标题"
          ></el-input>
        </el-form-item>
        <el-form-item
          label="详情部分-2-图-1-图片（380X285尺寸）"
          prop="paragraphContent2Img_1"
        >
          <pic-select
            v-model="form.paragraphContent2Img_1"
            placeholder="详情-1-图片"
          />
        </el-form-item>
        <el-form-item label="图片预览">
          <pic-container v-if="form.paragraphContent2Img_1" :url="form.paragraphContent2Img_1" />
        </el-form-item>
        <el-form-item
          label="详情部分-2-图-2-标题"
          prop="paragraphContent2ImgTitle_2"
        >
          <el-input
            v-model="form.paragraphContent2ImgTitle_2"
            placeholder="详情-2-图-2-标题"
          ></el-input>
        </el-form-item>
        <el-form-item
          label="详情部分-2-图-2-图片（380X285尺寸）"
          prop="paragraphContent2Img_2"
        >
          <pic-select
            v-model="form.paragraphContent2Img_2"
            placeholder="详情-2-图片"
          />
        </el-form-item>
        <el-form-item label="图片预览">
          <pic-container v-if="form.paragraphContent2Img_2" :url="form.paragraphContent2Img_2" />
        </el-form-item>
        <el-form-item
          label="详情部分-2-图-3-标题"
          prop="paragraphContent2ImgTitle_3"
        >
          <el-input
            v-model="form.paragraphContent2ImgTitle_3"
            placeholder="详情-2-图-3-标题"
          ></el-input>
        </el-form-item>
        <el-form-item
          label="详情部分-2-图-3-图片（380X285尺寸）"
          prop="paragraphContent2Img_3"
        >
          <pic-select
            v-model="form.paragraphContent2Img_3"
            placeholder="详情-2-图-3-图片"
          />
        </el-form-item>
        <el-form-item label="图片预览">
          <pic-container v-if="form.paragraphContent2Img_3" :url="form.paragraphContent2Img_3" />
        </el-form-item>
        <el-form-item label="案例展示图片（1106X622尺寸）" prop="img_1">
          <pic-select
            v-model="form.img_1"
            placeholder="案例展示图片"
          />
        </el-form-item>
        <el-form-item label="图片预览" v-if="form.img_1">
          <pic-container  :url="form.img_1" />
        </el-form-item>
        <el-form-item label="案例拓扑图（1000X761尺寸）" prop="img_2">
          <pic-select
            v-model="form.img_2"
            multiple
            placeholder="拓扑图片列表"
          />
        </el-form-item>
        <el-form-item label="图片预览" v-if="form.img_2.length">
          <pic-container v-for="(item,index) of form.img_2" :key="index" :url="item" />
        </el-form-item>
      </el-form>
      <template slot="footer">
        <el-button type="primary" @click="submit('caseForm')">保存</el-button>
      </template>
    </el-dialog>
  </div>
</template>
<script>
import { getCaseListApi, updateCaseListApi, deleteCaseApi } from "@/api/case";
import formValidatorMixins from "@/components/formValidatorMixins";
import picSelect from "@/components/picSelect";
import tableEditMixins from "@/components/tableEditMixins";
import PicContainer from "@/components/imgContainer";
export default {
  mixins: [formValidatorMixins, tableEditMixins],
  components: { picSelect ,PicContainer},
  data() {
    return {
      form: {
        id: "",
        title: "",
        titlePic: "",
        subTitle: "",
        navTitle:"",
        indexIcon:"",
        paragraphTitle_1: "",
        paragraphContent_1: "",
        paragraphImg_1: "",
        paragraphTitle_2: "",
        paragraphContent_2: "",
        paragraphContent2Img_1: "",
        paragraphContent2ImgTitle_1: "",
        paragraphContent2Img_2: "",
        paragraphContent2ImgTitle_2: "",
        paragraphContent2Img_3: "",
        paragraphContent2ImgTitle_3: "",
        img_1: '',
        img_2: []
      },
      rules: {
        navTitle:[
          {
            required:true,
            message:'导航栏标题必填',
            trigger:'blur'
          }
        ],
        indexIcon:[
          {
            required:true,
            message:'首页案例icon必选',
            trigger:"blur"
          }
        ],
        title: [
          {
            required: true,
            message: "标题必填",
            trigger: "blur"
          },
          {
            type: "string",
            max: 20,
            message: "最长20字符",
            trigger: "blur"
          }
        ],
        titlePic: [
          {
            required: true,
            message: "首页图片必选",
            trigger: "change"
          }
        ],
        subTitle: [
          {
            required: true,
            message: "简述必填",
            trigger: "blur"
          },
          {
            max: 50,
            type: "string",
            message: "最长50字符",
            trigger: "blur"
          }
        ],
        paragraphTitle_1: [
          {
            required: true,
            message: "段落1标题必填",
            trigger: "blur"
          },
          {
            type: "string",
            max: 100,
            message: "最长100字符",
            trigger: "blur"
          }
        ],
        paragraphContent_1: [
          {
            required: true,
            message: "段落1内容必填",
            trigger: "blur"
          },
          {
            type: "string",
            max: 500,
            message: "最长500字符",
            trigger: "blur"
          }
        ],
        paragraphImg_1: [
          {
            required: true,
            message: "段落1插图必选",
            trigger: "change"
          }
        ],
        paragraphTitle_2: [
          {
            required: true,
            message: "段落2标题必填",
            trigger: "blur"
          },
          {
            type: "string",
            max: 100,
            message: "最长100字符",
            trigger: "blur"
          }
        ],
        paragraphContent_2: [
          {
            required: true,
            message: "段落2内容必填",
            trigger: "blur"
          },
          {
            type: "string",
            max: 500,
            message: "最长500字符",
            trigger: "blur"
          }
        ],
        paragraphContent2Img_1: [
          {
            required: true,
            message: "图片必选",
            trigger: "change"
          }
        ],
        paragraphContent2ImgTitle_1: [
          {
            required: true,
            message: "图片标题必填",
            trigger: "change"
          },
          {
            type: "string",
            max: 50,
            message: "最长50字符",
            trigger: "blur"
          }
        ],
        paragraphContent2Img_2: [
          {
            required: true,
            message: "图片必选",
            trigger: "change"
          }
        ],
        paragraphContent2ImgTitle_2: [
          {
            required: true,
            message: "图片标题必填",
            trigger: "change"
          },
          {
            type: "string",
            max: 50,
            message: "最长50字符",
            trigger: "blur"
          }
        ],
        paragraphContent2Img_3: [
          {
            required: true,
            message: "图片必选",
            trigger: "change"
          }
        ],
        paragraphContent2ImgTitle_3: [
          {
            required: true,
            message: "图片标题必填",
            trigger: "change"
          },
          {
            type: "string",
            max: 50,
            message: "最长50字符",
            trigger: "blur"
          }
        ],
        img_1: [
          {
            required: true,
            message: "轮播图必填",
            trigger: "change"
          }
        ],
        img_2: [
          {
            required: true,
            message: "拓扑图必选",
            trigger: "change"
          },
          {
            type: "array",
            max: 2,
            message: "拓扑图最多2张",
            trigger: "change"
          }
        ]
      }
    };
  },
  methods: {
    initForm() {
      this.form = {
        id: "",
        title: "",
        titlePic: "",
        subTitle: "",
        paragraphTitle_1: "",
        paragraphContent_1: "",
        paragraphImg_1: "",
        paragraphTitle_2: "",
        paragraphContent_2: "",
        paragraphContent2Img_1: "",
        paragraphContent2ImgTitle_1: "",
        paragraphContent2Img_2: "",
        paragraphContent2ImgTitle_2: "",
        paragraphContent2Img_3: "",
        paragraphContent2ImgTitle_3: "",
        img_1: '',
        img_2: []
      };
    },
    getList(currentPage, pageSize) {
      return getCaseListApi({ currentPage, pageSize });
    },
    update(form) {
      return updateCaseListApi(form);
    },
    delete(id) {
      return deleteCaseApi(id);
    },
    resetForm() {
      this.$refs["caseForm"].clearValidate();
    }
  }
};
</script>
