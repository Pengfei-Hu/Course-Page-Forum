<template>
  <el-row class="student-manage" type="flex" justify="center">
    <el-col :md="24" :lg="23">
      <el-row type="flex" justify="space-between" class="mb-4">
        <div style="width: 250px">
          <data-table-search-bar
            v-model="searchText">
            </data-table-search-bar>
        </div>
        <div>
          <el-button style="display: none;"
          type="primary" @click="create">注册新学生</el-button>
        </div>
      </el-row>
      <el-row>
        <data-table
          :memberData="allStudents"
          @delete="deleteRow"
          @edit="editRow"
          :searchText="searchText"
          :tableFormat="memberTable">
        </data-table>
      </el-row>
      <el-dialog
        :title="form.id === '' ? '添加' : '编辑'"
        :visible.sync="dialogVisible"
        width="720px">
        <el-row type="flex" justify="center">
          <el-col :span="20">
            <el-form label-position="left" label-width="100px"
              :model="form" :rules="rules" ref="form">
              <el-form-item label="ID" v-show="form.id !== ''">
                <el-input v-model="form.id" :disabled="true"></el-input>
              </el-form-item>
              <el-form-item label="名字" prop="name">
                <el-input v-model="form.name"></el-input>
              </el-form-item>
              <el-form-item label="邮箱" prop="email">
                <el-input v-model="form.email"></el-input>
              </el-form-item>
              <el-form-item label="电话" prop="phone">
                <el-input v-model="form.phone"></el-input>
              </el-form-item>
              <el-form-item label="学院" prop="school">
                <el-input v-model="form.school"></el-input>
              </el-form-item>
            </el-form>
          </el-col>
        </el-row>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="submit(form.id)">确 定</el-button>
        </span>
      </el-dialog>
    </el-col>
  </el-row>
</template>

<script>
import DataTableSearchBar from '@/components/DataTableSearchBar.vue';
import DataTable from '@/components/DataTable.vue';
import { instance } from '@/helpers/instances';
import { memberTable } from '@/helpers/table';

export default {
  name: 'StudentManage',
  components: {
    DataTableSearchBar,
    DataTable,
  },
  data() {
    return {
      searchText: '',
      memberTable,
      form: {
        id: '',
        name: '',
        email: '',
        phone: '',
        school: '',
      },
      rules: {
        name: [
          { required: true, message: '请输入姓名', trigger: ['change', 'blur'] },
        ],
        email: [
          { required: true, message: '请输入邮箱地址', trigger: ['change', 'blur'] },
          { type: 'email', message: '请正确输入邮箱地址', trigger: ['change', 'blur'] },
        ],
        phone: [
          { required: true, message: '请输入电话', trigger: ['change', 'blur'] },
        ],
        school: [
          { required: true, message: '请输入学院', trigger: ['change', 'blur'] },
        ],
      },
      dialogVisible: false,
    };
  },
  computed: {
    allStudents() {
      return this.$store.getters.adminAllStudents;
    },
  },
  beforeCreate() {
    this.$store.dispatch('initAllStudents');
  },
  methods: {
    submit(id) {
      this.$refs.form.validate((valid) => {
        if (!valid) {
          this.$message.error('请正确填写完表格再提交！');
          return;
        }
        const url = id === '' ? '/api/v1/students' : `/api/v1/users/${id}`;
        const method = id === '' ? 'post' : 'put';
        instance({
          url,
          method,
          data: {
            ...this.form,
          },
        }).then((res) => {
          if (res.status !== 200) {
            return;
          }
          this.$store.dispatch('initAllStudents');
        }).catch((err) => {
          console.log(err);
        });
        this.dialogVisible = false;
      });
    },
    create() {
      this.form = {
        id: '',
        name: '',
        email: '',
        phone: '',
        school: '',
      };
      this.dialogVisible = true;
    },
    editRow(row) {
      this.form = { ...row };
      this.dialogVisible = true;
    },
    deleteRow(row) {
      this.$confirm(`删除${row.name}-${row.id}?`, '提示', {
        confirmButtonText: '确认',
        cancelButtonText: '取消',
        type: 'warning',
      }).then(() => {
        instance({
          url: `/api/v1/users/${row.id}`,
          method: 'delete',
        }).then((res) => {
          if (res.status !== 204) {
            return;
          }
          this.$store.dispatch('initAllStudents');
        }).catch((err) => {
          console.log(err);
        });
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除',
        });
      });
    },
  },
};
</script>

<style scoped>
.student-manage {
  flex: 1;
}
.student-manage-header {
  padding: 0 100px;
}
.mb-4 {
  margin-bottom: 10px;
}

</style>
