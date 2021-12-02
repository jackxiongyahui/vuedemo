<template>
  <div>
    <el-container style="border: 1px solid #eee">
      <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
        <el-menu>
          <el-submenu index="1">
            <template slot="title"
              ><i class="el-icon-message"></i>导航一</template
            >
            <el-menu-item-group>
              <el-menu-item index="1-1">选项1</el-menu-item>
              <el-menu-item index="1-2">选项2</el-menu-item>
            </el-menu-item-group>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-container>
        <el-header>
          <span style="margin-right: 20px">{{ time }}</span>
          <el-button
            type="primary"
            icon="el-icon-circle-plus"
            @click="addDialogVisible = true"
            >新 增</el-button
          >
          <el-dialog
            title="提示"
            :visible.sync="addDialogVisible"
            width="30%"
            append-to-body
          >
            <el-form label-position="right" label-width="80px" :model="addData">
              <el-form-item label="用户名">
                <el-input v-model="addData.username"></el-input>
              </el-form-item>
              <el-form-item label="密码">
                <el-input v-model="addData.password"></el-input>
              </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
              <el-button @click="addDialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="addUser(addData)"
                >确 定</el-button
              >
            </span>
          </el-dialog>
        </el-header>
        <el-main>
          <el-table :data="tableData" style="width: 100%" border>
            <el-table-column
              prop="id"
              label="id"
              align="center"
            ></el-table-column>
            <el-table-column
              prop="username"
              label="用户名"
              align="center"
            ></el-table-column>
            <el-table-column
              prop="password"
              label="密码"
              align="center"
            ></el-table-column>
            <el-table-column label="操作" align="center">
              <!-- 这里使用了插槽，相当于将一个模板插入到了这里 -->
              <template slot-scope="scope">
                <el-button
                  type="primary"
                  size="mini"
                  @click="editDialogButton(scope.$index, scope.row)"
                  >编辑</el-button
                >
                <el-button
                  type="primary"
                  size="mini"
                  @click="deleteButton(scope.$index, scope.row)"
                  >删除</el-button
                >
              </template>

              <el-dialog
                title="提示"
                :visible.sync="editDialogVisible"
                width="30%"
                append-to-body
              >
                <el-form
                  label-position="right"
                  label-width="80px"
                  :model="editData"
                >
                  <el-form-item label="用户名">
                    <el-input v-model="editData.username"></el-input>
                  </el-form-item>
                  <el-form-item label="密码">
                    <el-input v-model="editData.password"></el-input>
                  </el-form-item>
                </el-form>
                <span slot="footer" class="dialog-footer">
                  <el-button @click="editDialogVisible = false"
                    >取 消</el-button
                  >
                  <el-button type="primary" @click="editUser(editData)"
                    >确 定</el-button
                  >
                </span>
              </el-dialog>
            </el-table-column>
          </el-table>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data() {
    return {
      tableData: [],
      addDialogVisible: false,
      editDialogVisible: false,
      addData: { username: "", password: "" },
      editData: { username: "", password: "" },
      time: 0
    };
  },
  methods: {
    getList() {
      axios({
        method: "get",
        url: "./api/list",
      }).then((response) => {
        console.log(response.data);
        this.tableData = response.data;
      });
    },
    addUser(data) {
      axios({
        method: "post",
        url: "./api/insert",
        data: data,
      }).then(() => {
        this.getList();
        this.addDialogVisible = false;
      });
    },
    editDialogButton(index, data) {
      this.editData = data;
      this.editDialogVisible = true;
    },
    editUser(data) {
      axios({
        method: "post",
        url: "./api/update",
        data: data,
      }).then(() => {
        this.getList();
        this.editDialogVisible = false;
      });
    },
    deleteButton(index, data) {
      axios({
        method: "delete",
        url: `./api/delete/${data.id}`,
      }).then(() => {
        this.getList();
      });
    },
  },
  mounted() {
    this.getList();
    setInterval(() => {
      this.time = this.getTime;
    }, 500);
  },
  computed: {
    getTime() {
      return Date.now();
    },
  },
};
</script>

<style lang="less" scoped>
.el-header {
  text-align: right;
  font-size: 12px;
  background-color: #b3c0d1;
  color: #333;
  line-height: 60px;
}
</style>