<template>
    <div id="wapper">
    <h5>{{title}}</h5>
    <!-- {{customers}} -->
    {{customersIds}}
    <div style="margin-bottom: 10px;margin-top:10px">
      <el-button @click="dialogFormVisible = true;add()">添加</el-button>
      <el-button type="danger" @click="deletemoer()">批量删除</el-button>
    </div>
    <el-table :data="customers" style="width: 100%" @selection-change="handleSelectionChange">
    <!-- 这里的多选框已经被上面的代码给绑定过事件，所以不用:value="customers.id"的传递参数，但他获得的是整个所选中的全部属性和值，而我们要的是id所组成的数组，就要在多一步将传递的数据改变；*将数组对象转化为数组操作*-->
    <el-table-column type="selection">
    </el-table-column>
    <el-table-column prop="telephone" label="telephone">
    </el-table-column>
    <el-table-column prop="password" label="password">
    </el-table-column>
    <el-table-column prop="realname" label="realname">
    </el-table-column>
    <el-table-column prop="status" label="status">
    </el-table-column>
    <el-table-column prop="photo" label="photo">
    </el-table-column>
    <el-table-column label="操作">
    <template v-slot:default="scope">
        <!-- 如何传递参数给点击函数 -->
      <el-button size="mini" @click="dialogFormVisible = true;updata(scope.row)">修改</el-button>
      <el-button size="mini" type="danger" @click="deleteById(scope.row.id)">删除</el-button>
    </template>
    </el-table-column>
    </el-table>
    <el-dialog title="顾客详细信息" :visible.sync="dialogFormVisible">
    {{form}}
    <el-form :model="form">
        <el-input v-model="form.id" style="display:none"></el-input>
        <el-form-item label="顾客电话" :label-width="formLabelWidth">
        <el-input v-model="form.telephone" ></el-input>
        </el-form-item>
        <el-form-item label="顾客密码" :label-width="formLabelWidth">
        <el-input v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="姓名" :label-width="formLabelWidth">
        <el-input v-model="form.realname"></el-input>
        </el-form-item>
        <el-form-item label="状态" :label-width="formLabelWidth">
        <el-input v-model="form.status"></el-input>
        </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisible = false;submitModal()">确 定</el-button>
    </div>
    </el-dialog>
    </div>
</template>
<script>
import {get,post} from "../http/axios"
import axios from "axios"
import qs from "qs"
export default {
    data(){
        return{
            title:"顾客管理界面",
            customers:[{}],
            customersIds:[],
            customer:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            categoryIds:[],
            form:{}
        }
    },
    created(){
    this.reloadData();
    },
    methods:{
        reloadData(){
            axios.get('/customer/findAll').then((result)=>{
            this.customers=result.data;
            })
        },
        add(){
            this.form = {};
        },
        deletemoer(){
            let ids = this.customersIds;
            console.log(ids)
            axios.post('/customer/batchDelete',{ids:ids}).then((response)=>{
            this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.reloadData();
            });
        },
        deleteById(id){
            // console.log(id)
            axios.get('/customer/deleteById',{params:{id}}).then((response)=>{
            this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
            this.reloadData()
            });
        },
        updata(form){
            this.form=form;
            console.log(form);
        },
        async handleSelectionChange(val) {
            // 先获取数组对象中的id个数，在push进所创建的新数组中；
            let arr = val;
            var types = [];
            for(var i in arr){
                if(types.indexOf(arr[i].id) === -1){
                    types.push(arr[i].id)
                }
            }
                // 这个要放在for的外面，以免数据为空时数据的交接出错；
                this.customersIds = types;
        },
        submitModal(){
            this.customer = this.form;
            console.log(this.customer);
            axios.post("/customer/saveOrUpdate",this.customer).then((response)=>{
                this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.form = {};
                this.reloadData();
            });
        },
    }
}
</script>
<style scoped>

</style>