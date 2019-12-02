<template>
  <div>
    <!-- 查询表单 -->
    <el-form :model="params">
      <el-select v-model="params.siteId" placeholder="请选择站点">
        <el-option
          v-for="item in siteList"
          :key="item.siteId"
          :label="item.siteName"
          :value="item.siteId"
        ></el-option>
      </el-select>
      页面别名：<el-input v-model="params.pageAliase" style="width:100px"></el-input>
      页面名称：<el-input v-model="params.pageAliase" style="width:100px"></el-input>
      页面类型：
      <el-radio-group v-model="params.pageType">
        <el-radio class="radio" label="0">静态</el-radio>
        <el-radio class="radio" label="1">动态</el-radio>
      </el-radio-group>
      <el-button type="primary" size="small" v-on:click="query">查询</el-button>
      <router-link
        class="mui-tab-item"
        :to="{path:'/cms/page/add/',query:{
        page: this.params.page, 
        siteId: this.params.siteId
      }}"
      >
        <el-button type="primary" size="small">新增页面</el-button>
      </router-link>
    </el-form>
    <el-table :data="list" style="width: 100%">
      <el-table-column prop="pageName" label="页面名称" width="120"></el-table-column>
      <el-table-column prop="pageAliase" label="别名" width="120"></el-table-column>
      <el-table-column prop="pageType" label="页面类型" width="100" align="center"></el-table-column>
      <el-table-column prop="pageWebPath" label="访问路径" width="250"></el-table-column>
      <el-table-column prop="pagePhysicalPath" label="物理路径" width="250"></el-table-column>
      <el-table-column prop="pageCreateTime" label="创建时间" width="180"></el-table-column>
      <el-table-column label="操作" width="450" align="center" fixed="right">
          <template slot-scope="scope">
            <el-button size="small" type="primary" @click="edit(scope.row.pageId)">编辑</el-button>
            <el-button size="small" type="danger"  @click="del(scope.row.pageId)">删除</el-button>
            <el-button size="small" type="primary"  @click="tohtml(scope.row.pageId)">静态化</el-button>
            <el-button size="small" type="primary"  @click="preview(scope.row.pageId)">页面预览</el-button>
            <el-button size="small" type="primary"  @click="preview(scope.row.pageId)">页面发布</el-button>
          </template>
        </el-table-column>
    </el-table>
    <el-pagination
      layout="prev, pager, next"
      :total="total"
      :page-size="params.size"
      :current-page="params.page"
      v-on:current-change="changePage"
      style="float:right"
    ></el-pagination>
  </div>
</template>

<script>
  import * as cmsApi from '../api/cms'
  export default {
    data() {
      return {
        siteList:[],
        list: [],
          total:0,
          params:{
            siteId:'',
            pageId:'',
            pageAliase:'',
            page:1,
            size:10
          }
        }
      },
      methods:{
        query:function(){
          // alert('查询')
          //调用服务端的接口
          cmsApi.page_list(this.params.page,this.params.size,this.params).then((res)=>{
            //将res结果数据赋值给数据模型对象
            this.list = res.queryResult.list;
            this.total = res.queryResult.total;
          })

        },
        // 分页查询方法
        changePage:function(page){//形参就是当前页码
          //调用query方法
          this.params.page = page;
          this.query()
        },
        // 修改
        edit:function(pageId){
          this.$router.push({ path: '/cms/page/edit/'+pageId,query:{
            page: this.params.page,
            siteId: this.params.siteId}})
        },
        //删除
        del:function(pageId) {
          this.$confirm('确认删除该记录吗?', '提示', {
            type: 'warning'
          }).then(() => {
            this.listLoading = true;
            // let pageId = row.pageId;
            cmsApi.page_del(pageId).then((res) => {
              this.listLoading = false;
              if(res.success){
                this.$message.success("删除成功")
                this.query();
              }else{
                this.$message.error('删除失败');
              }

            });
          }).catch(() => {

          });
        }
      
      },
      mounted(){
        //当DOM元素渲染完成后调用query
        this.query()
        // 初始化站点列表
        // cmsApi.site_list().then((res)=>{
        //     //将res结果数据赋值给数据模型对象
        //     this.siteList = res.queryResult;
        //   })
        this.siteList = [
          {
            siteId:'5a751fab6abb5044e0d19ea1',
            siteName:'门户主站'
          },{
            siteId:'102',
            siteName:'测试站'
          }
        ]
      }
  }
</script>
