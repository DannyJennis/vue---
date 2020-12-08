<template>
  <div class="order">
    <el-card style="">
      <div class="aa">
        <img src="../Login/images/20201003110432.png" class="imgg" />
        <b><big> 您正在支付订单，请谨防钓鱼链接或诈骗电话</big></b>
      </div>
      <hr />
      <span style="float: left"><b> 选择收货人信息：</b> </span><br />
      <el-select v-model="taData.msg" placeholder="收货地址选择" size="min">
        <el-option
          v-for="(item, id) in this.taData"
          :key="id"
          :label="item.msg"
          :value="item.msg"
        >
        </el-option>
      </el-select>
      <input
        type="text"
        v-model="taData.msg"
        readonly="readonly"
        style="width: 450px; height: 40px; margin-lift: 100px; border: none"
      />
      <el-button
        type="primary"
        size="mini"
        @click="dialogVisible = true"
        style="width: 205px;height:40px;margin-right=50px;float:right"
        >新增地址</el-button
      >
      <hr />
      <span style="float: left"><b>选择支付方式：</b> </span>
      <br />
      <el-radio-group v-model="radio1">
        <el-radio-button label="货到付款"></el-radio-button>
        <el-radio-button label="在线支付"></el-radio-button>
        <el-radio-button label="银行汇款"></el-radio-button>
      </el-radio-group>
      <input
        type="text"
        v-model="radio1"
        readonly="readonly"
        style="height: 40px; margin-lift: 100px; border: none"
      />

      <br />

      <hr />
      <span style="float: left"><b>发票信息</b> </span>
      <br />
      <el-radio v-model="radio" label="不需要发票" border>不需要发票</el-radio>
      <el-radio v-model="radio" label="增值税普通发票" border
        >增值税普通发票</el-radio
      >
      <el-radio v-model="radio" label="电子发票" border>电子发票</el-radio>
      <input
        type="text"
        v-model="radio"
        readonly="readonly"
        style="height: 40px; margin-lift: 100px; border: none"
      />
      <hr />
      <span style="float: left"><b>送货清单：</b> </span>
      <br />
      <div style="size：40px;color: red">
        <br />
        <span>商品收货信息：{{ taData.msg }}</span>
        <br />
        <span>支付方式：{{ radio1 }}</span> <br />
        <span>发票信息：{{ radio }}</span>
        <br />
        <span>商品种数：{{ this.$route.params.totalAmount }}</span>
        <br />
        <span>应付金额：￥{{ this.$route.params.totalPrice }}</span>
        <div style="text-align: center">
          <button class="btn submitForm" style="color: red" @click="btn()">
            支付订单
          </button>
        </div>
      </div>
    </el-card>

    <br />

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

<script type="text/javascript">
import { getAdderss_add, addAddress } from "../../api/index";

export default {
  data() {
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
      code: "",
      radio: "",
      radio1: "",
      taData: [],
      dialogVisible: false,
      addAddressForm: {
        address_id: "",
        user_name: "",
        user_phone: "",
        user_address: "",
      },
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
  onLoad: function (options) {
    //刚进入页面随机先获取一个
    this.createCode();
  },
  components: {},
  mounted() {
    this.getAdderss_add();
  },
  methods: {
    createCode() {
      for (let i = 0; i < 9; i++) {
        let code = Math.round(Math.random() * 5);
        console.log(code);
      }
    },
    addDialogClosed() {
      this.$refs.addUserAddressRef.resetFields();
    },
    async getAdderss_add() {
      const results = await getAdderss_add();
      if (results.success_code === 200) {
        this.taData = results.message;
        console.log(this.taData);
      }
    },
    btn() {
      
        this.$confirm('支付成功，是否跳转订单页面', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(async () => { 
          this.$router.push({path:'/me/sales'})
       } ).catch(() => {
          this.$message({
            type: 'info',
            message: '已拒绝跳转'
          });
        })
      },

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
            this.getAdderss_add();
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

<style lang="stylus" scoped>
.imgg {
  width: 100px;
}

.aa {
  text-align: center;
}
</style>
