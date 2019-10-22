<template>
    <div id="wapper">
    <h5>{{title}}</h5>
    <!-- {{orders}} -->
    {{ordersIds}}
    <div style="margin-bottom: 10px;margin-top:10px">
      <el-button @click="dialogFormVisible = true;add()">添加</el-button>
      <el-button type="danger" @click="deletemoer()">批量删除</el-button>
    </div>
    <el-table :data="orders" style="width: 100%" @selection-change="handleSelectionChange">
    <!-- 这里的多选框已经被上面的代码给绑定过事件，所以不用:value="orders.id"的传递参数，但他获得的是整个所选中的全部属性和值，而我们要的是id所组成的数组，就要在多一步将传递的数据改变；*将数组对象转化为数组操作*-->
    <el-table-column type="selection">
    </el-table-column>
    <el-table-column prop="customerId" label="customerId">
    </el-table-column>
    <el-table-column prop="waiterId" label="waiterId">
    </el-table-column>
    <el-table-column prop="addressId" label="addressId">
    </el-table-column>
    <el-table-column prop="orderTime" label="orderTime">
    </el-table-column>
    <el-table-column prop="total" label="total">
    </el-table-column>
    <el-table-column prop="status" label="status">
    </el-table-column>
    <el-table-column prop="remark" label="remark">
    </el-table-column>
    <el-table-column label="操作">
    <template v-slot:default="scope">
        <!-- 如何传递参数给点击函数 -->
      <el-button size="mini" @click="dialogFormVisible = true;updata(scope.row)">修改</el-button>
      <el-button size="mini" type="danger" @click="deleteById(scope.row.id)">删除</el-button>
    </template>
    </el-table-column>
    </el-table>
    <el-dialog title="详细信息" :visible.sync="dialogFormVisible">
    {{form}}
    <el-form :model="form">
        <el-form-item label="总数：" :label-width="formLabelWidth">
        <el-input v-model="form.total" ></el-input>
        </el-form-item>
        <el-form-item label="顾客id" :label-width="formLabelWidth">
        <el-select v-model="form.customerId" placeholder="">
            <el-option v-for="item in customerIds" :key="item.id" :value="item.id">{{item.realname}}</el-option>
        </el-select>
        </el-form-item>
        <el-form-item label="服务员id" :label-width="formLabelWidth">
        <el-select v-model="form.waiterId" placeholder="">
            <el-option v-for="item in waiterIds" :key="item.id" :value="item.id">{{item.id}}</el-option>
        </el-select>
        </el-form-item>
        <el-form-item label="地址id" :label-width="formLabelWidth">
        <el-select v-model="form.addressId" placeholder="">
            <el-option v-for="item in addressIds" :key="item.id" :value="item.id">{{item.id}}</el-option>
        </el-select>
        </el-form-item>
        <el-input v-model="form.commentTime" style="display:none"></el-input>
        <el-form-item label="status" :label-width="formLabelWidth">
        <el-select v-model="form.status" placeholder="">
            <el-option value="未接单">未接单</el-option>
            <el-option value="待服务">待服务</el-option>
            <el-option value="服务完成">服务完成</el-option>
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
            title:"订单管理界面",
            orders:[{}],
            ordersIds:[],
            order:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            customerIds:[],
            waiterIds:[],
            addressIds:[],
            form:{}
        }
    },
    created(){
    this.reloadData();
    },
    methods:{
        reloadData(){
            axios.get('/order/findAll').then((result)=>{
            this.orders=result.data;
            })
        },
        add(){
            this.form = {};
            this.reloadDatacustomerIds();
            this.reloadDatawaiterIds();
            this.reloadDataaddressIds();
        },
        deletemoer(){
            let ids = this.ordersIds;
            console.log(ids)
            axios.post('/order/batchDelete',{ids:ids}).then((response)=>{
            this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.reloadData();
            });
        },
        deleteById(id){
            // console.log(id)
            axios.get('/order/deleteById',{params:{id}}).then((response)=>{
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
                this.ordersIds = types;
        },
        submitModal(){
            let myDate = new Date();
            let mytime=myDate.getTime();
            this.form.commentTime=mytime;
            this.order = this.form;
            console.log(this.order);
            axios.post("/order/saveOrUpdate",this.order).then((response)=>{
                this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.form = {};
                this.reloadData();
            });
        },
        reloadDatacustomerIds(){
            axios.get('/customer/findAll').then((result)=>{
                this.customerIds = result.data;
            })
        },
        reloadDatawaiterIds(){
            axios.get('/waiter/findAll').then((result)=>{
                this.waiterIds = result.data;
            })
        },
        reloadDataaddressIds(){
            axios.get('/address/findAll').then((result)=>{
                this.addressIds = result.data;
            })
        },
    }
}
</script>
<style scoped>

</style>