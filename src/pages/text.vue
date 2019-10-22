<template>
    <div id="wapper">
    {{productsIds}}
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
    </div>
</template>
<script>
import axios from "axios"
import qs from "qs"
    axios.defaults.baseURL="http://134.175.100.63:6677";
    // axios.interceptors.response.use(function(response){
    //   response.status = response.data.status;
    //   response.statusText = response.data.message;
    //   response.data = response.data.data;
    //   return response;
    // });
    axios.defaults.headers.post["Content-Type"] = "application/x-www-form-urlencoded"
    axios.defaults.transformRequest = [function(data){
      // 默认的indices类型，传递的参数将会报错；
      // qs.stringify({ids: [1, 2, 3]}, {arrayFormat: ‘indices‘})
      //形式： ids[0]=1&aids1]=2&ids[2]=3
      // qs.stringify({ids: [1, 2, 3]}, {arrayFormat: ‘repeat‘})
      //形式： ids=1&ids=2&id=3
      return qs.stringify(data,{arrayFormat :'repeat'});
    }]
export default {
    name:'wapper',
    data(){
        return{
            products:[{}],
            productsIds:[],
        }
    },
    created(){
    this.reloadData();
    },
    methods:{
        reloadData(){
            axios.get('/product/findAll').then((result)=>{
            this.products=result.data.data;
            })
        },
        deleteById(id){
        
        },
        updata(){
           
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
    }
}
</script>
<style scoped>

</style>