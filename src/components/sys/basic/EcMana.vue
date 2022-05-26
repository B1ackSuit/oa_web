<template>
  <div>
    <div class="header-title">奖惩对应的工资帐套选择：</div>
    <div style="margin-top: 20px" v-for="(punInfo, index) in punInfos" :key="index"  class="text item">
      <el-link type="danger" disabled>{{punInfo}}</el-link>
    </div>
  </div>
</template>
<script>
export default {
  name: "EcMana",
  data() {
    return {
      punInfos: [
        ' 月迟到3天内',
        ' 月迟到3-5天',
        ' 月绩效不合格'
      ],
    }
  },
  methods: {}
}
</script>

<style scoped>

</style>
