<template>
  <div id="admin-users">
    <el-table :data="tableData" style="width: 100%">
      <el-table-column prop="address_id" label="邮政编码"> </el-table-column>
      <el-table-column prop="user_name" label="用户姓名"> </el-table-column>
      <el-table-column prop="user_phone" label="用户手机号"> </el-table-column>
      <el-table-column prop="user_address,user_address" label="收货地址"> </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="props">
          <el-button type="primary" size="mini" @click="dialogVisible = true"
            >添加地址</el-button
          >
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(props.$index, props.row)"
            >删除地址</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <el-dialog
      title="添加地址"
      :visible.sync="dialogVisible"
      width="50%"
      @close="addDialogClosed"
    >
      <el-form
        :model="addAddressForm"
        :rules="addUserAddressRules"
        ref="addUserAddressRef"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="邮政编码" prop="address_id">
          <el-input v-model="addAddressForm.address_id"></el-input>
        </el-form-item>
        <el-form-item label="用户姓名" prop="user_name">
          <el-input v-model="addAddressForm.user_name"></el-input>
        </el-form-item>
        <el-form-item label="用户手机号" prop="user_phone">
          <el-input v-model="addAddressForm.user_phone"></el-input>
        </el-form-item>
        <el-form-item label="收货地址" prop="user_address">
          <el-input v-model="addAddressForm.user_address"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addAddress('addAddressForm')"
          >确 定</el-button
        >
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { getAdderss, deleteAddress, addAddress } from "../../../api/index";
export default {
  data() {
    // 手机正则验证
    var checkMobile = (rule, value, cb) => {
      const regMobile = /^[1][3,4,5,7,8][0-9]{9}$/;
      if (regMobile.test(value)) {
        return cb();
      }
      cb(new Error("请输入合法的手机号"));
    };
    // 邮政编码正则验证
    var checkAddid = (rule, value, cb) => {
      const regAddid = /^[1-9][0-9]{5}$/;
      if (regAddid.test(value)) {
        return cb();
      }
      cb(new Error("请输入合法的邮政编码"));
    };
    return {
      tableData: [],
      dialogVisible: false,
      addAddressForm: {
        address_id: "",
        user_name: "",
        user_phone: "",
        user_address: "",
      },
      // 字段名验证规则
      addUserAddressRules: {
        address_id: [
          { required: true, message: "请输入邮政编码", trigger: "blur" },
          {
            validator: checkAddid,
            trigger: "blur",
          },
        ],
        user_name: [
          { required: true, message: "请输入用户名", trigger: "blur" },
          {
            require: true,
            trigger: "blur",
          },
        ],
        user_phone: [
          { required: true, message: "请输入手机号", trigger: "blur" },
          { validator: checkMobile, trigger: "blur" },
        ],
        user_address: [
          { required: true, message: "请输入收货地址", trigger: "blur" },
          { trigger: "blur" },
        ],
      },
    };
  },
  mounted() {
    this.getAdderss();
  },
  methods: {
    addDialogClosed() {
      this.$refs.addUserAddressRef.resetFields();
    },
    // 获取地址
    async getAdderss() {
      const results = await getAdderss();
      if (results.success_code === 200) {
        this.tableData = results.message;
      }
    },
    // 删除地址
    async handleDelete(index, row) {
      this.$confirm("您确定删除该地址吗?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(async () => {
          let result = await deleteAddress(row.address_id);
          if (result.success_code === 200) {
            this.$message({
              type: "success",
              message: "已删除",
            });
            this.getAdderss();
          }
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
    // 增加地址
    async addAddress() {
      this.$confirm("您确定添加该地址吗?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(async () => {
          let result = await addAddress(
            this.addAddressForm.address_id,
            this.addAddressForm.user_name,
            this.addAddressForm.user_phone,
            this.addAddressForm.user_address
          );
          // alert(JSON.stringify(result));
          if (result.success_code === 200) {
            this.$message({
              type: "success",
              message: "已添加",
            });
            this.getAdderss();
          } else {
            this.$message({
              type: "info",
              message: "添加失败",
            });
          }
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消添加",
          });
        });
    },
  },
};
</script>

<style scoped>
</style>
