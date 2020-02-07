<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <!-- 第一行 -->
        <el-row :gutter="20">
          <el-col :span="6">
            学科
            <el-select v-model="searchForm.subjectID" placeholder="请选择" class="wh">
              <el-option
                v-for="item in subjectIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            难&nbsp;&nbsp;&nbsp;&nbsp;度
            <el-select v-model="searchForm.difficulty" placeholder="请选择" class="wh">
              <el-option
                v-for="item in difficultyList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            试题类型
            <el-select v-model="searchForm.questionType" placeholder="请选择" class="wh">
              <el-option
                v-for="item in questionTypeList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            标&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;签
            <el-select v-model="searchForm.tags" placeholder="请选择" class="wh">
              <el-option
                v-for="item in tagsList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
        </el-row>
        <!-- 第二行 -->
        <el-row :gutter="20">
          <el-col :span="6">
            城市
            <el-select
              v-model="searchForm.province"
              placeholder="选城市"
              style="width:109px"
              @change="searchForm.city=''"
            >
              <el-option v-for="item in provinces()" :key="item" :label="item" :value="item"></el-option>
            </el-select>
            <el-select v-model="searchForm.city" placeholder="选区县" style="width:109px">
              <el-option
                v-for="item in citys(searchForm.province)"
                :key="item"
                :label="item"
                :value="item"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            关键字
            <el-input v-model="searchForm.keyword" placeholder="请输入题目编号/题干" class="wh"></el-input>
          </el-col>
          <el-col :span="6">
            题目备注
            <el-input v-model="searchForm.remarks" placeholder="请输入" class="wh"></el-input>
          </el-col>
          <el-col :span="6">
            企业简称
            <el-input v-model="searchForm.shortName" placeholder="请输入" class="wh"></el-input>
          </el-col>
        </el-row>
        <!-- 第三行 -->
        <el-row :gutter="20">
          <el-col :span="6">
            方向
            <el-select v-model="searchForm.direction" placeholder="请选择" class="wh">
              <el-option v-for="item in directionList" :key="item" :label="item" :value="item"></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            录入人
            <el-select v-model="searchForm.creatorID" placeholder="请选择" class="wh">
              <el-option
                v-for="item in creatorIDList"
                :key="item.id"
                :label="item.username"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            二级目录
            <el-select v-model="searchForm.catalogID" placeholder="请选择" class="wh">
              <el-option
                v-for="item in catalogIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            <el-button size="mini">清除</el-button>
            <el-button size="mini" type="primary">搜索</el-button>
          </el-col>
        </el-row>
        <!-- 基础试题列表 -->
        <el-table :data="questionList" style="width:100%">
          <el-table-column label="序号" type="index" width="60"></el-table-column>
          <el-table-column label="试题编号" prop="number"></el-table-column>
          <el-table-column label="学科" prop="subject"></el-table-column>
          <el-table-column :formatter="questionTypeFMT" label="题型" prop="questionType"></el-table-column>
          <el-table-column label="题干" prop="question"></el-table-column>
          <el-table-column label="录入时间" prop="addDate" width="170">
            <span slot-scope="stData">{{stData.row.addDate|parseTimeByString}}</span>
          </el-table-column>
          <el-table-column :formatter="difficultyFMT" label="难度" prop="difficulty"></el-table-column>
          <el-table-column label="录入人" prop="creator"></el-table-column>
          <el-table-column label="操作" width="200">
            <template slot-scope="stData">
              <a href="#">预览</a>
              <a href="#">修改</a>
              <a href="#" @click.prevent="del(stData.row)">删除</a>
              <a href="#">加入精选</a>
            </template>
          </el-table-column>
        </el-table>
      </el-card>
    </div>
  </div>
</template>

<script>
// 导入api
import { list, remove } from '@/api/hmmm/questions' // 基础题库相关api导入  删除基础试题api
import { provinces, citys } from '@/api/hmmm/citys' // 城市  区县
import { simple as usersSimple } from '@/api/base/users' // 录入人
import { simple as directorysSimple } from '@/api/hmmm/directorys' // 二级目录
import { simple as tagsSimple } from '@/api/hmmm/tags' // 标签
import { simple } from '@/api/hmmm/subjects' // 学科api
// 导入 题型  难度  方向
// as给导入的成员设置别名
import {
  direction as directionList,
  difficulty as difficultyList,
  questionType as questionTypeList
} from '@/api/hmmm/constants' // 常量数据
export default {
  name: 'QuestionsList',
  data() {
    return {
      // 定义各个搜索表单域的数据展示成员
      questionList: [], // 基础题库列表信息
      catalogIDList: [], // 二级目录
      creatorIDList: [], // 录入人
      tagsList: [], // 标签
      subjectIDList: [], // 学科列表
      directionList, // 方向 简易成员赋值
      difficultyList, // 难度 简易成员赋值
      questionTypeList, // 试题类型 简易成员赋值
      // 定义搜索数据对象
      searchForm: {
        subjectID: '', // 学科
        difficulty: '', // 难度
        questionType: '', // 类型
        tags: '', // 标签
        province: '', // 城市
        city: '', // 区县
        keyword: '', // 关键字
        remarks: '', // 备注
        shortName: '', // 企业简称
        direction: '', // 方向
        creatorID: '', // 录入人
        catalogID: '' // 二级目录
      }
    }
  },
  watch: {
    // 对 searchForm 做深度监听
    searchForm: {
      handler(newV, oldV) {
        // 重新获得基础试题
        this.getQuestionList()
      },
      deep: true
    }
  },
  created() {
    this.getQuestionList() // 基础题库列表
    this.getCatalogIDList() // 二级目录列表
    this.getCreatorIDList() // 录入人列表
    this.getTagsList() // 标签
    this.getSubjectIDList() // 学科列表
    // console.log(this.difficultyList)  // 查看 难度
    // console.log(this.questionTypeList) // 查看 题型
    // console.log(this.directionList) // 查看 方向  ["o2o", "外包服务",....]
    // console.log(this.provinces()) // 查看 城市
    // console.log(this.citys()) // 查看 区县
  },
  methods: {
    provinces, // 城市 简易成员赋值 provinces:provinces
    citys, // 区县 简易成员赋值
    // 删除试题操作
    del(question) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(async () => {
          this.$message.success('删除成功!')
          let res = await remove(question)
          // console.log(res)
          // 刷新列表
          this.getQuestionList()
        })
        .catch(() => {})
    },
    // 对 难度 进行二次更新操作
    difficultyFMT(row, column, cellValue, index) {
      // 把对应的的汉字返回
      return this.difficultyList[cellValue - 1].label
    },
    // 对 题型 进行二次更新操作
    questionTypeFMT(row, column, cellValue, index) {
      // console.log(cellValue)   3  2 ...
      // 把对应的的汉字返回
      return this.questionTypeList[cellValue - 1].label
    },
    // 基础题库列表
    async getQuestionList() {
      // 给list传递数据检索的条件信息
      let res = await list(this.searchForm)
      // console.log(res)
      this.questionList = res.data.items
    },
    // 二级目录列表
    async getCatalogIDList() {
      let res = await directorysSimple()
      // console.log(res)
      this.catalogIDList = res.data
    },
    // 录入人列表
    async getCreatorIDList() {
      let res = await usersSimple()
      // console.log(res)
      this.creatorIDList = res.data
    },
    // 标签列表
    async getTagsList() {
      let res = await tagsSimple()
      // console.log(res)
      this.tagsList = res.data
    },
    // 学科列表
    async getSubjectIDList() {
      let res = await simple()
      //  console.log(res)
      this.subjectIDList = res.data
    }
  }
}
</script>

<style scoped>
.el-table {
  margin-top: 20px;
}
.el-input {
  width: 223px;
}
.el-row {
  margin-bottom: 10px;
}
</style>
