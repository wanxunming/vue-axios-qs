<template>
    <div id="wapper">
    <h5>{{title}}</h5>
    <!-- {{addresss}} -->
    {{addresssIds}}
    <div style="margin-bottom: 10px;margin-top:10px">
      <el-button @click="dialogFormVisible = true;add()">添加</el-button>
      <el-button type="danger" @click="deletemoer()">批量删除</el-button>
    </div>
    <el-table :data="addresss" style="width: 100%" @selection-change="handleSelectionChange">
    <!-- 这里的多选框已经被上面的代码给绑定过事件，所以不用:value="addresss.id"的传递参数，但他获得的是整个所选中的全部属性和值，而我们要的是id所组成的数组，就要在多一步将传递的数据改变；*将数组对象转化为数组操作*-->
    <el-table-column type="selection">
    </el-table-column>
    <el-table-column prop="province" label="province">
    </el-table-column>
    <el-table-column prop="city" label="city">
    </el-table-column>
    <el-table-column prop="area" label="area">
    </el-table-column>
    <el-table-column prop="address" label="address">
    </el-table-column>
    <el-table-column prop="telephone" label="telephone">
    </el-table-column>
    <el-table-column prop="customerId" label="customerId">
    </el-table-column>
    <el-table-column label="操作">
    <template v-slot:default="scope">
        <!-- 如何传递参数给点击函数 -->
      <el-button size="mini" @click="dialogFormVisible = true;updata(scope.row)">修改</el-button>
      <el-button size="mini" type="danger" @click="deleteById(scope.row.id)">删除</el-button>
    </template>
    </el-table-column>
    </el-table>
    <el-dialog title="评价详细信息" :visible.sync="dialogFormVisible">
    {{form}}
    <el-form :model="form">
        <el-form-item label="省份：" :label-width="formLabelWidth">
        <el-input v-model="form.province" ></el-input>
        </el-form-item>
        <el-form-item label="城市：" :label-width="formLabelWidth">
        <el-input v-model="form.city" ></el-input>
        </el-form-item>
        <el-form-item label="地区：" :label-width="formLabelWidth">
        <el-input v-model="form.area" ></el-input>
        </el-form-item>
        <el-form-item label="地址：" :label-width="formLabelWidth">
        <el-input v-model="form.address" ></el-input>
        </el-form-item>
        <el-form-item label="电话：" :label-width="formLabelWidth">
        <el-input v-model="form.telephone" ></el-input>
        </el-form-item>
        <el-form-item label="顾客id：" :label-width="formLabelWidth">
        <el-select v-model="form.customerId" placeholder="">
            <!-- 一定要绑定key值，否则将报错 -->
            <el-option v-for="item in customerIds" :key="item.id" :value="item.id">{{item.realname}}</el-option>
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
            title:"地址管理界面",
            addresss:[{}],
            addresssIds:[],
            address:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            customerIds:[],
            form:{}
        }
    },
    created(){
    this.reloadData();
    },
    methods:{
        reloadData(){
            axios.get('/address/findAll').then((result)=>{
            this.addresss=result.data;
            })
        },
        add(){
            this.form = {};
            this.reloadDatecustomerId();
        },
        deletemoer(){
            let ids = this.addresssIds;
            console.log(ids)
            axios.post('/address/batchDelete',{ids:ids}).then((response)=>{
            this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.reloadData();
            });
        },
        deleteById(id){
            axios.get('/address/deleteById',{params:{id}}).then((response)=>{
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
                this.addresssIds = types;
        },
        submitModal(){
            this.address = this.form;
            console.log(this.address);
            axios.post("/address/saveOrUpdate",this.address).then((response)=>{
                this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.form = {};
                this.reloadData();
            });
        },
        // 下拉框绑定数据：
        reloadDatecustomerId(){
            axios.get('/customer/findAll').then((result)=>{
                this.customerIds = result.data;
            })
        },
    }
}
</script>
<style scoped>

</style>