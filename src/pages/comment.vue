<template>
    <div id="wapper">
    <h5>{{title}}</h5>
    <!-- {{comments}} -->
    {{commentsIds}}
    <div style="margin-bottom: 10px;margin-top:10px">
      <el-button @click="dialogFormVisible = true;add()">添加</el-button>
      <el-button type="danger" @click="deletemoer()">批量删除</el-button>
    </div>
    <el-table :data="comments" style="width: 100%" @selection-change="handleSelectionChange">
    <!-- 这里的多选框已经被上面的代码给绑定过事件，所以不用:value="comments.id"的传递参数，但他获得的是整个所选中的全部属性和值，而我们要的是id所组成的数组，就要在多一步将传递的数据改变；*将数组对象转化为数组操作*-->
    <el-table-column type="selection">
    </el-table-column>
    <el-table-column prop="content" label="content">
    </el-table-column>
    <el-table-column prop="commentTime" label="commentTime">
    </el-table-column>
    <el-table-column prop="orderId" label="orderId">
    </el-table-column>
    <el-table-column label="操作">
    <template v-slot:default="scope">
        <!-- 如何传递参数给点击函数 -->
      <el-button size="mini" @click="dialogFormVisible = true;updata(scope.row)">
          <i class="el-icon-edit-outline"></i>
      </el-button>
      <el-button size="mini" type="danger" @click="deleteById(scope.row.id)">
          <i class="el-icon-delete"></i>
      </el-button>
    </template>
    </el-table-column>
    </el-table>
    <el-dialog title="评价详细信息" :visible.sync="dialogFormVisible">
    {{form}}
    <el-form :model="form">
        <el-form-item label="评价内容" :label-width="formLabelWidth">
        <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="form.content"></el-input>
        </el-form-item>
        <el-input v-model="form.commentTime" style="display:none"></el-input>
        <el-form-item label="订单id" :label-width="formLabelWidth">
        <el-select v-model="form.orderId" placeholder="">
            <!-- 一定要绑定key值，否则将报错 -->
            <el-option v-for="item in orderIds" :key="item.id" :value="item.id">{{item.id}}</el-option>
        </el-select>
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
            title:"评价管理界面",
            comments:[{}],
            commentsIds:[],
            comment:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            orderIds:[],
            form:{}
        }
    },
    created(){
    this.reloadData();
    },
    methods:{
        reloadData(){
            axios.get('/comment/findAll').then((result)=>{
            this.comments=result.data;
            })
        },
        add(){
            this.reloadDateorderId();
            this.form = {};
        },
        deletemoer(){
            let ids = this.commentsIds;
            // console.log(ids)
            axios.post('/comment/batchDelete',{ids:ids}).then((response)=>{
            this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.reloadData();
            });
        },
        deleteById(id){
            // console.log(id)
            axios.get('/comment/deleteById',{params:{id}}).then((response)=>{
            this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
            this.reloadData()
            });
        },
        updata(form){
            this.form=form;
            // console.log(form);
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
                this.commentsIds = types;
        },
        submitModal(){
            let myDate = new Date();
            let mytime=myDate.getTime();
            // 如何将数据直接传递到表单的输入框，
            this.form.commentTime=mytime;
            this.comment = this.form;
            console.log(this.comment);
            axios.post("/comment/saveOrUpdate",this.comment).then((response)=>{
                this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.form = {};
                this.reloadData();
            });
        },
        reloadDateorderId(){
            axios.get('/order/findAll').then((response)=>{
                this.orderIds = response.data;
            })
        },
    }
}
</script>
<style scoped>

</style>