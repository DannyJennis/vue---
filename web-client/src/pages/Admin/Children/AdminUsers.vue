<template>
  <div id="admin-users">
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="id" label="用户ID"> </el-table-column>
      <el-table-column prop="user_name" label="账号"> </el-table-column>
      <el-table-column prop="user_phone" label="手机"> </el-table-column>
      <el-table-column prop="user_nickname" label="昵称"> </el-table-column>
      <el-table-column prop="user_address" label="住址"> </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="props">
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(props.$index, props.row)"
            >注销用户</el-button
          >
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { getAllUsers } from "../../../api/index";
import { deleteUser } from "./../../../api/index";
export default {
  data() {
    return {
      tableData: [],
    };
  },
  mounted() {
    this.getUsers();
  },
  methods: {
    async getUsers() {
      const results = await getAllUsers();
      if (results.success_code === 200) {
        this.tableData = results.message;
      }
    },
    async handleDelete(index, row) {
      
      this.$confirm("您确定注销该用户吗?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(async () => {
          let result = await deleteUser(row.id);
          if (result.success_code === 200) {
            this.$message({
              type: "success",
              message: "已删除",
            });
            this.getUsers();
          }
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
  },
};
</script>

<style scoped>
#admin-users{
   background:#F0FFF0
}
</style>
