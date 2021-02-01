<template>
  <div class="app-container">
    <el-row>
      <el-button type="primary" @click="handleAdd">添加帮助</el-button>
    </el-row>
    <div class="table-box">
      <el-table :data="tableData" border>
        <el-table-column label="标题" prop="title" show-overflow-tooltip />
        <el-table-column label="发布时间" prop="time" sortable ></el-table-column>
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
      title="帮助详情"
      :close-on-click-modal="false"
      center
      width="900px"
      :visible.sync="dialogShow"
    >
      <el-form ref="helpForm" :model="form" :rules="rules" label-position="top">
        <el-form-item label="标题" prop="title">
          <el-input
            v-model="form.title"
            type="textarea"
            placeholder="输入帮助标题"
          />
        </el-form-item>
         <el-form-item label="帮助内容" prop="content">
          <tinymece ref="tinymece" v-model="form.content" />
        </el-form-item>
        <el-form-item label="预览" v-if="form.content">
          <div class="pre-content" v-html="form.content"></div>
        </el-form-item>
      </el-form>
      <template slot="footer">
        <el-button type="primary" @click="submit('helpForm')">保存</el-button>
      </template>
    </el-dialog>
  </div>
</template>
<script>
import { getHelpListApi, updateHelpListApi, deleteHelpApi } from "@/api/help";
import formValidatorMixins from "@/components/formValidatorMixins";
import picSelect from "@/components/picSelect";
import tableEditMixins from "@/components/tableEditMixins";
import Tinymece from '@/components/tinymece';
export default {
  mixins: [formValidatorMixins, tableEditMixins],
  components: { picSelect,Tinymece },
  data() {
    return {
      form: {
        id: "",
        title: "",
        content: ""
      },
      rules: {
        title: [
          {
            type: "string",
            required: true,
            message: "标题必填",
            trigger: "blur"
          },
          {
            min: 0,
            max: 100,
            message: "标题不能超过100字符",
            trigger: "blur"
          }
        ],
        content: [
          {
            type: "string",
            required: true,
            message: "内容必填",
            trigger: "blur"
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
        content: ""
      };
    },
    getList(currentPage, pageSize) {
      return getHelpListApi({ currentPage, pageSize });
    },

    update(form) {
      return updateHelpListApi(form);
    },
    delete(id) {
      return deleteHelpApi(id);
    },
    resetForm() {
      this.$refs["helpForm"].clearValidate();
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
