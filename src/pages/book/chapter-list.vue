<template>
    <div>
        <!--工具栏-->
        <el-form :inline="true" size="mini" class="toolbar">
            <el-form-item>
                <el-button type="primary" @click="gotoAdd" >新增</el-button>
            </el-form-item>
        </el-form>
        <!--表格区-->
        <el-table :data="tableData" border style="width: 100%;" size="small">
            <template slot="empty">
                还没有数据呢~ (⊙︿⊙)
            </template>
            <el-table-column prop="sortNumber" label="章序" width="90" align="center">
            </el-table-column>
            <el-table-column prop="name" label="章节名称"  >
            </el-table-column>
            <el-table-column label="锁章状态" width="90" align="center">
                <template  slot-scope="scope" >
                    <i :class="handleStatus(scope.row.lockStatus)"></i>
                </template>
            </el-table-column>
            <el-table-column align="center" label="操作" width="240">
            <template slot-scope="scope" >
                <el-button size="mini" type="primary" plain 
                @click="handleDetails(scope.row.id)">查看</el-button>
                <el-button size="mini" type="warning" plain 
                @click="handleEdit(scope.row.id)">编辑</el-button>
                <el-button size="mini" type="danger"  plain 
                @click="handleDelete(scope.row.id)">删除</el-button>
            </template>
            </el-table-column>
        </el-table>
        <!--分页区-->
        <div class="Pagination" style="text-align: left; margin-top: 10px;">
            <el-pagination
                background
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-size="limit"
                layout="total, prev, pager, next"
                :total="total">
            </el-pagination>
        </div>
    </div>
</template>

<script>
  export default {
    data() {
        return {
            bookId:'',
            tableData: [],
            limit: 10,
            total: 0,
            currentPage: 1
        }
    },
    created(){
        this.getListData();
        // 图书ID
        this.bookId = this.$route.params.bookId;
    },
    methods:{
        handleStatus(val){
            let icon = "el-icon-unlock";
            if(val){
                icon = "el-icon-lock";
            }
            return icon;
        },
        handleDetails(id){
            this.$router.push("/book/book-read/"+id);
        },
        handleEdit(id) {
            this.$router.push('/book/chapter-edit/'+id);
        },
        handleDelete(id) {
            this.$confirm('确定要删除吗?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.deleteRequest('/book-chapter/delete',{id:id}).then(resp => {
                    if (resp.code == 200) {
                        this.getListData();
                    }
                })
            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '已取消操作'
                });
            });
            
        },
        getListData(){
            this.getRequest('/book-chapter/get-list', {page:this.currentPage,limit:this.limit,bookId:this.$route.params.bookId}).then(resp => {
                if (resp.code == 200) {
                    this.tableData = resp.data;
                    this.total = resp.total;
                }
            })
        },
        handleSizeChange(val) {
            console.log(`每页 ${val} 条`);
        },
        handleCurrentChange(val) {
            this.currentPage = val;
            this.getListData();
        },
        onSearch(){
            this.getListData();
        },
        gotoAdd(){
            this.$router.push("/book/chapter-add/"+this.bookId);
        }
    }
  };
</script>

<style scoped>
    .toolbar{
        height: 40px;
    }
</style>