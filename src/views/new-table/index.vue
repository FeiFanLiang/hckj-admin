<template>
  <div class="app-container">
    <el-row>
      <el-button type="primary" @click="handleAdd">添加新闻</el-button>
    </el-row>
    <div class="table-box" v-loading="loading">
      <el-table :data="tableData" border>
        <el-table-column label="新闻标题" prop="title" show-overflow-tooltip />
        <el-table-column
          label="新闻简述"
          prop="subTitle"
          show-overflow-tooltip
        />
        <el-table-column label="类型" sortable prop="type" :formatter="formatterType" />
        <el-table-column label="发布时间" sortable  prop="time"></el-table-column>
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
    <el-dialog
      :close-on-click-modal="false"
      title="新闻详情"
      center
      width="900px"
      :visible.sync="dialogShow"
    >
      <el-form ref="newForm" :model="form" :rules="rules" label-position="top">
        <el-form-item label="新闻标题" prop="title">
          <el-input
            v-model="form.title"
            type="textarea"
            placeholder="输入新闻标题"
          />
        </el-form-item>
        <el-form-item label="标题图片（375X225）" prop="titlePic">
          <pic-select
            filterable
            v-model="form.titlePic"
            placeholder="选择标题图片"
          />
        </el-form-item>
        <el-form-item label="图片预览" v-if="form.titlePic">
          <pic-container :url="form.titlePic" />
        </el-form-item>
        <el-form-item label="新闻简介" prop="subTitle">
          <el-input
            v-model="form.subTitle"
            type="textarea"
            placeholder="输入新闻简介"
          />
        </el-form-item>
        <el-form-item label="新闻类型">
          <el-select v-model="form.type" placeholder="选择新闻类型">
            <el-option
              v-for="item of types"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="是否首页展示">
          <el-switch v-model="form.indexShow"></el-switch>
        </el-form-item>
        <el-form-item label="新闻内容" prop="content">
          <tinymece ref="tinymece" v-model="form.content" />
        </el-form-item>
        <el-form-item label="新闻预览" v-if="form.content">
          <div class="pre-content" v-html="form.content"></div>
        </el-form-item>
      </el-form>
      <template slot="footer">
        <el-button type="primary" @click="submit('newForm')">保存</el-button>
      </template>
    </el-dialog>
  </div>
</template>
<script>
import { getNewListApi, updateNewApi, deleteNewApi } from "@/api/news";
import formValidatorMixins from "@/components/formValidatorMixins";
import picSelect from "@/components/picSelect";
import tableEditMixins from "@/components/tableEditMixins";
import PicContainer from '@/components/imgContainer';
import Tinymece from '@/components/tinymece';
export default {
  mixins: [formValidatorMixins, tableEditMixins],
  components: { picSelect,PicContainer,Tinymece },
  data() {
    return {
      tableData: [],
      total: 0,
      currentPage: 1,
      pageSize: 30,
      dialogShow: false,
      loading: false,
      form: {
        id: "",
        title: "",
        subTitle: "",
        type: 1,
        titlePic: "",
        content: "",
        indexShow: true
      },
      rules: {
        title: [
          {
            required: true,
            message: "标题必填",
            trigger: "blur"
          },
          {
            type: "string",
            max: 50,
            message: "长度不能大于50字符",
            trigger: "blur"
          }
        ],
        subTitle: [
          {
            required: true,
            message: "简述必填",
            trigger: "blur"
          },
          {
            type: "string",
            max: 300,
            message: "长度不能大于300字符",
            trigger: "blur"
          }
        ],
        titlePic: [
          {
            required: true,
            message: "图片必选",
            trigger: "change"
          }
        ],
        content: [
          {
            required: true,
            message: "内容必填",
            trigger: "blur"
          }
        ]
      },
      types: [
        {
          value: 1,
          label: "公司新闻"
        },
        {
          value: 2,
          label: "行业新闻"
        }
      ]
    };
  },
  created() {
    this.fetchData();
  },
  methods: {
    formatterType(value) {
      if (value === 1) {
        return "公司新闻";
      }
      return "行业新闻";
    },

    initForm() {
      this.form = {
        id: "",
        title: "",
        subTitle: "",
        type: 1,
        titlePic: "",
        content: "",
        indexShow: true
      };
    },

    getList(currentPage, pageSize) {
      return getNewListApi({ currentPage, pageSize });
    },

    update(form) {
      return updateNewApi(form);
    },
    delete(id) {
      return deleteNewApi(id);
    },
    resetForm() {
      this.$refs["newForm"].clearValidate();
    }
  }
};
</script>
<style lang="scss">
.pre-content {
   p {
     color: #333333;
     line-height: 28px;
     font-size: 16px;
   }
}
</style>
