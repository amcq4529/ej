<template>
  <div>   
    <!-- 按钮 -->
  <div>
            <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
  </div>
    <!-- /按钮 -->
    <!-- 表格 -->
            <el-table :data="products">
<el-table-column fixed="left" prop="id" label="编号"></el-table-column>
<el-table-column fixed="left" prop="name" label="产品名称"></el-table-column>
<el-table-column prop="price" label="价格"></el-table-column>
<el-table-column prop="description" label="描述"></el-table-column>
<el-table-column prop="categoryId" label="所属产品"></el-table-column>

<el-table-column fixed="right" label="操作">
  <template v-slot="slot">
           <a href="" @click.prevent="toDeleteHandler(slot,row,id)" >删除</a>
          <a href="" @click.prevent="toUpdataHandler(slot,row,id)">修改</a>
    <!-- 显示脚本数据 -->
    <!-- {{slot}} -->
  </template>
</el-table-column>
        </el-table>
    <!-- /表格 -->
    <!-- 分页 -->
      <!--分页开始-->
  <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
<!--/分页结束-->
    <!-- /分页 -->
    <!-- 模态框 -->
    <el-dialog
  :title="录入产品信息"
  :visible.sync="visible"
  width="60%">
  <el-form label-width="80px">

    <el-form-item label="名称">
      <el-input v-model="form.name"/>
    </el-form-item>
    <el-form-item label="价格">
      <el-input v-model="form.num"/>
    </el-form-item>

<el-form-item label="所属栏目">
                    <el-select v-model="form.status" placeholder="请选择">
                        <el-option v-for="item in options"
                            :key="item.value" :label="item.name"
                            :value="item.parentId">
                        </el-option>
                    </el-select>
                </el-form-item>
  </el-form>
    <el-form-item label="介绍">
      <el-input v-model="form.description"/>
    </el-form-item> 
  <span slot="footer" class="dialog-footer">
    <el-button size="small" type="success" @click="closeModelHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
    <!-- /模态框 -->



    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    
    methods:{
      loadData(){
              // vue文档创建完毕所执行操作
      let url="http://localhost:6677/product/findAll"
      request.get(url).then((response)=>{
        //箭头函数中的this指向外部函数中的this
        // 将查询结果设置到customers中，this指向外部函数的this
        this.products = response.data;
        
      })
      },
      submitHandler(){
     //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
      // 通过request与后台进行交互，并且要携带参数
        let url="http://localhost:6677/product/saveOrUpdate"
        //前端向后台发送请求，保存数据的保存操作
        request({
            url,
          method:"POST",
           header:{
           "content-Type":"application/x-www-form-urlencoded"
          },
       //数据回调 then 返回结果
      data:querystring.stringify(this.form)
        }).then((response)=>{
          //请求结束
          this.closeModelHandler();
                  // 刷新
        this.loadData();
        // 提示消息
          this.$message({
          type:"success",
          message:response.message
        })
        })

      },
            toUpdataHandler(){
              this.title="修改员工信息";
this.visible = true;
      },
       toDeleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
        },
      toAddHandler(){
         let url = "http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
                this.options = response.data;
            })
            this.title="添加产品信息",
            this.visible=true;
      },
      closeModelHandler(){
         this.visible = false;
      }
    },
    data(){
      return {
        visible:false,
        products:[],
        form:{
          type:"product"
        }
      }
    },
    created(){
    this.loadData();
    }
}
</script>
<style scoped>

</style>