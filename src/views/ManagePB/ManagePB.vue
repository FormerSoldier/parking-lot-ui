<template>
  <div>
    <div class="admin-container">
      <div class="handle-box">
        <el-input v-show="false" v-model="select_word" placeholder="搜索" class="handle-input mr10"></el-input>
        <el-button v-show="false" type="primary" icon="el-icon-search" @click="selectPB">搜索</el-button>
        <el-button v-show="false" type="primary" @click="getAllPBList">查看全部</el-button>
        <el-button class="add" type="primary" @click="handleAdd">添加</el-button>
      </div>
      <el-table :data="getPbList.slice((currentPage - 1) * pagesize, currentPage * pagesize)" border class="table">
        <el-table-column prop="name" label="姓名" width="240"></el-table-column>
        <el-table-column prop="gender" label="性别" width="240"></el-table-column>
        <el-table-column prop="joinTime" label="入职时间">
          <template slot-scope="scope">
            {{ moment(scope.row.joinTime).format('YYYY-MM-DD HH:mm:ss') }}
          </template>
        </el-table-column>
        <el-table-column label="操作" width="180" align="center">
          <template slot-scope="scope">
            <el-button type="text" icon="el-icon-edit" @click="handleEdit(scope.row)">编辑</el-button>
            <el-button type="text" icon="el-icon-delete" class="red" @click="handleDelete(scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <div class="admin-pagination">
        <el-pagination
          background
          :page-size="pagesize"
          :current-page="currentPage"
          @current-change="handleCurrentChange"
          layout="prev, pager, next"
          :total="getPbList.length"
        ></el-pagination>
      </div>
    </div>

    <PBAdd :addVisible.sync="addVisible"></PBAdd>
    <PbEdit :editVisible.sync="editVisible" :form="form"></PbEdit>
    <PbDel :delVisible="delVisible" :currentPage="currentPage" @cancleDel="cancleDel($event)" :id="id"></PbDel>
  </div>
</template>

<script>
import PBAdd from './PbAdd';
import PbEdit from './PbEdit';
import PbDel from './PbDel';
import moment from 'moment';

export default {
  name: 'basetable',
  mounted: function() {
    this.$store.dispatch('fetchAllPb');
  },
  components: {
    PBAdd,
    PbEdit,
    PbDel,
  },
  data() {
    return {
      moment,
      pbList: [],
      currentPage: 1,
      pagesize: 5,
      select_word: '',
      editVisible: false,
      delVisible: false,
      addVisible: false,
      id: '',
      form: {
        id: '',
        name: '',
        gender: '',
        joinTime: '',
      },
    };
  },
  computed: {
    getPbList() {
      return this.$store.getters.getPbList;
    },
  },
  methods: {
    handleCurrentChange(val) {
      this.currentPage = val;
    },
    getAllPBList() {
      this.pbList = this.getPbList;
    },
    selectPB() {
      this.pbList = this.$store.getters.getPbListByName(this.select_word);
    },
    handleAdd() {
      this.addVisible = true;
    },
    handleEdit(row) {
      this.id = row.id;
      this.form = {
        id: row.id,
        name: row.name,
        gender: row.gender,
        joinTime: row.joinTime,
      };
      this.editVisible = true;
    },
    handleDelete(row) {
      this.id = row.id;
      this.delVisible = true;
    },
    cancleDel(val) {
      this.delVisible = false;
      this.currentPage = val;
    },
  },
};
</script>

<style scoped>
.add {
  float: right;
}

.handle-box {
  margin-bottom: 20px;
}

.handle-select {
  width: 120px;
}

.handle-input {
  width: 300px;
  display: inline-block;
}

.del-dialog-cnt {
  font-size: 16px;
  text-align: center;
}

.table {
  width: 100%;
  font-size: 14px;
}

.red {
  color: #ff0000;
}

.mr10 {
  margin-right: 10px;
}
</style>
