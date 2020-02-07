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
            <el-select v-model="searchForm.province" placeholder="选城市" style="width:109px">
              <!-- <el-option
                v-for="item in subjectIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>-->
            </el-select>
            <el-select v-model="searchForm.city" placeholder="选区县" style="width:109px">
              <!-- <el-option
                v-for="item in subjectIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>-->
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
              <!-- <el-option
                v-for="item in subjectIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>-->
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
      </el-card>
    </div>
  </div>
</template>

<script>
// 导入api
import { simple as usersSimple } from '@/api/base/users' // 录入人
import { simple as directorysSimple } from '@/api/hmmm/directorys' // 二级目录
import { simple as tagsSimple } from '@/api/hmmm/tags' // 标签
import { simple } from '@/api/hmmm/subjects' // 学科api
// 导入 题型  难度
// as给导入的成员设置别名
import {
  difficulty as difficultyList,
  questionType as questionTypeList
} from '@/api/hmmm/constants' // 常量数据
export default {
  name: 'QuestionsList',
  data() {
    return {
      // 定义各个搜索表单域的数据展示成员
      catalogIDList: [], // 二级目录
      creatorIDList: [], // 录入人
      tagsList: [], // 标签
      subjectIDList: [], // 学科列表
      difficultyList, // 简易成员赋值
      questionTypeList, // 简易成员赋值
      // 定义搜索数据对象
      searchForm: {
        subjectID: '', // 学科
        difficulty: '', // 难度
        questionType: '', // 类型
        tags: '', // 标签
        province: '', // 城市
        city: '', // 地区
        keyword: '', // 关键字
        remarks: '', // 备注
        shortName: '', // 企业简称
        direction: '', // 方向
        creatorID: '', // 录入人
        catalogID: '' // 二级目录
      }
    }
  },
  created() {
    this.getCatalogIDList() // 二级目录列表
    this.getCreatorIDList() // 录入人列表
    this.getTagsList() // 标签
    this.getSubjectIDList() // 学科列表
    // console.log(this.difficultyList)  // 查看 难度
    // console.log(this.questionTypeList) // 查看 题型
  },
  methods: {
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
.el-input {
  width: 223px;
}
.el-row {
  margin-bottom: 10px;
}
</style>
