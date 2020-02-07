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
            难度
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
            标签
            <!-- <el-select v-model="value" placeholder="请选择" class="wh"> -->
              <!-- <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option> -->
            <!-- </el-select> -->
          </el-col>
        </el-row>
      </el-card>
    </div>
  </div>
</template>

<script>
// 导入api
// 学科api
import { simple } from '@/api/hmmm/subjects'
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
      subjectIDList: [], // 学科列表
      difficultyList, // 简易成员赋值
      questionTypeList, // 简易成员赋值
      // 定义搜索数据对象
      searchForm: {
        subjectID: '', // 学科
        difficulty: '', // 难度
        questionType: '' // 类型
      }
    }
  },
  created() {
    // 学科列表
    this.getSubjectIDList()
    // console.log(this.difficultyList)  // 查看 难度
    // console.log(this.questionTypeList) // 查看 题型
    
  },
  methods: {
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

</style>
