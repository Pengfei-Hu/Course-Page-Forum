<template>
  <div>
    <!-- This is TeacherSemesterList component. -->
    <el-row :gutter="40">
      <el-col :span="8" v-for="(item, i) in courses" :key="i">
          <el-button type="primary"
          @click="toManage(item)"
          plain>
              {{item.semester}}
          </el-button>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  name: 'SemesterListView',
  computed: {
    courses() {
      return this.$store.getters.courseSemesters(this.$route.params.cname);
    },
  },
  methods: {
    toManage(item) {
      document.title = `${this.$route.params.cname}-${item.semester}`;
      this.$router.push({
        name: 'TeacherCourseManage',
        params: {
          tid: this.$route.params.tid,
          cname: this.$route.params.cname,
          sname: item.semester,
          cid: item.cid,
        },
      });
    },
  },
  mounted() {
    document.title = this.$route.params.cname;
  },
};
</script>

<style scoped>
.el-row {
  width: 1000px;

}
.el-button {
  height: 120px;
  width: 100%;
  font-size: 20px;
  margin-bottom: 30px;
}
</style>
