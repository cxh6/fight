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
            <el-select v-model="addForm.enterpriseID"></el-select>
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
        </el-form>
      </el-card>
    </div>
  </div>
</template>

<script>
import { simple } from '@/api/hmmm/subjects' // 学科
import { simple as directorysSimple } from '@/api/hmmm/directorys' // 二级目录
import { provinces, citys } from '@/api/hmmm/citys' // 城市  区县
// 导入  方向
import { direction as directionList } from '@/api/hmmm/constants' // 常量数据
export default {
  name: 'QuestionsNew',
  data() {
    return {
      directionList, // 方向 简易成员赋值
      subjectIDList: [], // 学科列表
      catalogIDList: [], // 二级目录
      addForm: {
        // 如下表单字段名称来自yapi数据接口
        subjectID: '', // 学科
        catalogID: '', // 二级目录
        enterpriseID: '', // 企业
        city: '', // 区县
        province: '', // 城市
        direction: '' // 方向
      }
    }
  },
  created() {
    this.getCatalogIDList() // 二级目录列表
    this.getSubjectIDList() // 学科列表
  },
  methods: {
    provinces, // 城市 简易成员赋值 provinces:provinces
    citys, // 区县 简易成员赋值
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
