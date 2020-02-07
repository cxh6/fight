<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="box-card">
        <el-form ref="addFormRef" :model="addForm" label-width="120px">
          <!--:model作用：可以使得el-form针对表单的全部信息进行收集，固定属性-->
          <el-form-item label="学科：">
            <el-select v-model="addForm.subjectID">
              <el-option
                v-for="item in subjectIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="目录：">
            <el-select v-model="addForm.catalogID">
              <el-option
                v-for="item in catalogIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="企业：">
            <el-select v-model="addForm.enterpriseID">
              <el-option
                v-for="item in enterpriseIDList"
                :key="item.id"
                :label="item.shortName"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="城市：">
            <el-select v-model="addForm.province" @change="addForm.city=''">
              <el-option v-for="item in provinces()" :key="item" :label="item" :value="item"></el-option>
            </el-select>
            <el-select v-model="addForm.city">
              <el-option
                v-for="item in citys(addForm.province)"
                :key="item"
                :label="item"
                :value="item"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="方向：">
            <el-select v-model="addForm.direction">
              <el-option v-for="item in directionList" :key="item" :label="item" :value="item"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="题型：">
            <el-radio-group v-model="addForm.questionType">
              <el-radio
                v-for="item in questionTypeList"
                :key="item.value"
                :label=" item.value+'' "
              >{{item.label}}</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="难度：">
            <el-radio-group v-model="addForm.difficulty">
              <el-radio
                v-for="item in difficultyList"
                :key="item.value"
                :label=" item.value+'' "
              >{{item.label}}</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="题干：">
            <el-input type="textarea" v-model="addForm.question"></el-input>
          </el-form-item>
          <el-form-item label="选项：">
            <br />
            <el-radio v-model="singleSelect" :label="0">
              A:
              <el-input v-model="addForm.options[0].title"></el-input>
            </el-radio>
            <br />
            <el-radio v-model="singleSelect" :label="1">
              B:
              <el-input v-model="addForm.options[1].title"></el-input>
            </el-radio>
            <br />
            <el-radio v-model="singleSelect" :label="2">
              C:
              <el-input v-model="addForm.options[2].title"></el-input>
            </el-radio>
            <br />
            <el-radio v-model="singleSelect" :label="3">
              D:
              <el-input v-model="addForm.options[3].title"></el-input>
            </el-radio>
          </el-form-item>
          <el-form-item label="答案：">
            <el-input type="textarea" v-model="addForm.answer"></el-input>
          </el-form-item>
          <el-form-item label="备注：">
            <el-input type="textarea" v-model="addForm.remarks"></el-input>
          </el-form-item>
          <el-form-item label="标签：">
            <el-input type="text" v-model="addForm.tags"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary">提交</el-button>
          </el-form-item>
        </el-form>
      </el-card>
    </div>
  </div>
</template>

<script>
import { list } from '@/api/hmmm/companys' // 学科
import { simple } from '@/api/hmmm/subjects' // 学科
import { simple as directorysSimple } from '@/api/hmmm/directorys' // 二级目录
import { provinces, citys } from '@/api/hmmm/citys' // 城市  区县
// 导入  方向、题型、难度
import {
  difficulty as difficultyList,
  direction as directionList,
  questionType as questionTypeList
} from '@/api/hmmm/constants' // 常量数据
export default {
  name: 'QuestionsNew',
  data() {
    return {
      singleSelect: '',
      difficultyList, // 难度 (简易成员赋值)
      questionTypeList, // 题型 (简易成员赋值)
      enterpriseIDList: [], // 企业列表
      directionList, // 方向 简易成员赋值
      subjectIDList: [], // 学科列表
      catalogIDList: [], // 二级目录
      // 如下表单字段名称来自yapi数据接口
      addForm: {
        options: [
          // {code: '编号ABCD', title: '当前项目文字答案',
          //   img: '当前项目图片答案', isRight: boolean表明当前项目是否是答案},
          { code: 'A', title: '', img: '', isRight: false },
          { code: 'B', title: '', img: '', isRight: false },
          { code: 'C', title: '', img: '', isRight: false },
          { code: 'D', title: '', img: '', isRight: false }
        ],
        difficulty: '1', // 默认“单选” 难度 项目被选中(要求是字符串)
        questionType: '1', // 默认“单选” 题型 项目被选中(要求是字符串)
        subjectID: '', // 学科
        catalogID: '', // 二级目录
        enterpriseID: '', // 企业
        city: '', // 区县
        province: '', // 城市
        direction: '', // 方向
        question: '', // 题干
        answer: '', // 答案
        remarks: '', // 备注
        tags: '', // 标签
        videoURL: 'http://www.xxx.com' // 解析视频
      }
    }
  },
  // watch监听器
  watch: {
    // 监听选项选中要把isRight的值做设置操作
    singleSelect(newval) {
      // 要使得全部的isRight变为false
      // newval代表的当前项目的isRight变为true
      for (var i = 0; i < 4; i++) {
        this.addForm.options[i].isRight = false
      }
      this.addForm.options[newval].isRight = true
    }
  },
  created() {
    this.getCatalogIDList() // 二级目录列表
    this.getSubjectIDList() // 学科列表
    this.getEnterpriseIDList() // 企业列表
  },
  methods: {
    provinces, // 城市 简易成员赋值 provinces:provinces
    citys, // 区县 简易成员赋值
    // 企业列表
    async getEnterpriseIDList() {
      let res = await list()
      // console.log(res)
      this.enterpriseIDList = res.data.items
    },
    // 二级目录列表
    async getCatalogIDList() {
      let res = await directorysSimple()
      // console.log(res)
      this.catalogIDList = res.data
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
</style>
