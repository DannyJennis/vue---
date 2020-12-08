<template>
  <div id="admin-sales">
    <el-card>
      <el-row>
        <el-col>
          <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="id" label="订单编号"> </el-table-column>
            <el-table-column prop="user_name" label="商品收货信息"> </el-table-column>
            <el-table-column prop="user_phone" label="支付方式"> </el-table-column>
            <el-table-column prop="user_nickname" label="应付金额">
            </el-table-column>
            
            <el-table-column label="操作">
              <template slot-scope="props">
                <el-button
                  size="mini"
                  type="danger"
                  @click="handleDelete(props.$index, props.row)"
                  >解除订单</el-button
                >
              </template>
            </el-table-column>
          </el-table>
        </el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script>
export default {};
</script>

<style scoped>
</style>
