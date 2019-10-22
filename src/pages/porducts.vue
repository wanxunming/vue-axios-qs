<template>
    <div id="wapper">
    <h5>{{title}}</h5>
    <!-- {{product}} -->
    {{productsIds}}
    <div style="margin-bottom: 10px;margin-top:10px">
      <el-button @click="dialogFormVisible = true;add()">添加</el-button>
      <el-button type="danger" @click="deletemoer()">批量删除</el-button>
    </div>
    <el-table :data="products" style="width: 100%" @selection-change="handleSelectionChange">
    <!-- 这里的多选框已经被上面的代码给绑定过事件，所以不用:value="products.id"的传递参数，但他获得的是整个所选中的全部属性和值，而我们要的是id所组成的数组，就要在多一步将传递的数据改变；*将数组对象转化为数组操作*-->
    <el-table-column type="selection">
    </el-table-column>
    <el-table-column prop="name" label="name">
    </el-table-column>
    <el-table-column prop="description" label="description">
    </el-table-column>
    <el-table-column prop="price" label="price">
    </el-table-column>
    <el-table-column prop="status" label="status">
    </el-table-column>
    <el-table-column prop="photo" label="photo">
    </el-table-column>
    <el-table-column prop="categoryId" label="categoryId">
    </el-table-column>
    <el-table-column prop="" label="操作">
    <template v-slot:default="scope">
        <!-- 如何传递参数给点击函数 -->
      <el-button size="mini" @click="dialogFormVisible = true;updata(scope.row)" type="primary" icon="el-icon-edit" circle></el-button>
      <el-button size="mini" type="danger" @click="deleteById(scope.row.id)" icon="el-icon-delete" circle></el-button>
    </template>
    </el-table-column>
    </el-table>
    <el-dialog title="产品详细信息" :visible.sync="dialogFormVisible">
    {{form}}
    <el-form :model="form">
        <el-input v-model="form.id" style="display:none"></el-input>
        <el-form-item label="产品名称" :label-width="formLabelWidth">
        <el-input v-model="form.name" ></el-input>
        </el-form-item>
        <el-form-item label="产品价钱" :label-width="formLabelWidth">
        <el-input v-model="form.price"></el-input>
        </el-form-item>
        <el-form-item label="状态" :label-width="formLabelWidth">
        <el-input v-model="form.status"></el-input>
        </el-form-item>
        <el-form-item label="产品描述" :label-width="formLabelWidth">
        <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="form.description">
        </el-input>
        </el-form-item>
        <el-form-item label="产品id" :label-width="formLabelWidth">
        <el-select v-model="form.categoryId" placeholder="">
            <!-- 一定要绑定key值，否则将报错 -->
            <el-option v-for="item in categoryIds" :key="item.id" :value="item.id">{{item.id}}</el-option>
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
    // axios.defaults.baseURL="http://134.175.100.63:6677";
    // // axios.interceptors.response.use(function(response){
    // //   response.status = response.data.status;
    // //   response.statusText = response.data.message;
    // //   response.data = response.data.data;
    // //   return response;
    // // });
    // axios.defaults.headers.post["Content-Type"] = "application/x-www-form-urlencoded"
    // axios.defaults.transformRequest = [function(data){
    //   // 默认的indices类型，传递的参数将会报错；
    //   // qs.stringify({ids: [1, 2, 3]}, {arrayFormat: ‘indices‘})
    //   //形式： ids[0]=1&aids1]=2&ids[2]=3
    //   // qs.stringify({ids: [1, 2, 3]}, {arrayFormat: ‘repeat‘})
    //   //形式： ids=1&ids=2&id=3
    //   return qs.stringify(data,{arrayFormat :'repeat'});
    // }]
export default {
    data(){
        return{
            title:"产品管理界面",
            products:[{}],
            productsIds:[],
            product:{},
            dialogFormVisible: false,
            formLabelWidth: '120px',
            categoryIds:[],
            form:{}
            // form到product的参数传递可以，但是可能因为数据类型不符合后台的要求，报出外键约束问题；
            // 不知道product到form参数传递是否可行；
        }
    },
    created(){
    this.reloadData();
    // setTimeout(()=>{
    // this.reloadData();
    // },5000)
    },
    methods:{
        reloadData(){
            axios.get('/product/findAll').then((result)=>{
            this.products=result.data;
            })
        },
        add(){
            this.form = {};
            this.reloadDataOrderIds();
        },
        deletemoer(){
            let ids = this.productsIds;
            console.log(ids)
            axios.post('/product/batchDelete',{ids:ids}).then((response)=>{
            this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.reloadData();
            });
        },
        deleteById(id){
            axios.get('/product/deleteById',{params:{id}}).then((response)=>{
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
                this.productsIds = types;
        },
        //表单提交按钮的绑定函数： 会报外键约束问题
        // 可能是id：没有传递；
        submitModal(){
            this.product = this.form;
            console.log(this.product);
            axios.post("/product/saveOrUpdate",this.product).then((response)=>{
                this.$message({ message: '操作成功'+response.statusText, type: 'success'});
            }).catch((error)=>{
                this.$message.error('错误信息：'+error);
            }).finally(()=>{
                this.form = {};
                this.reloadData();
            });
        },
        // 加载产品类名数据到表单的下拉框；
        reloadDataOrderIds(){
            axios.get('/category/findAll').then((result)=>{
                this.categoryIds = result.data;
            })
        }
    }
}
</script>
<style scoped>

</style>