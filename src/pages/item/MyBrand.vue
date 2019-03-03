<template>
    <v-card>
      <v-card-title class="layout row">
        <v-btn color="primary" @click="show=true">
          新增品牌
        </v-btn>
        <!-- 填充中间的空隙-->
        <v-spacer/>
        <!-- 搜索框-->
        <v-text-field
          class="flex sm3"
            label="Search"
            v-model="search"
            append-icon="search"
        ></v-text-field>
<!--
分割线
-->
      </v-card-title>

      <v-divider/>
      <v-data-table
        :headers="headers"
        :items="brands"
        :pagination.sync="pagination"
        :total-items="totalBrands"
        :loading="loading"
        class="elevation-1"
      >
        <template slot="items" slot-scope="props">
          <td class="text-xs-center">{{ props.item.id}}</td>
          <td class="text-xs-center">{{ props.item.name}}</td>
          <td class="text-xs-center"><img :src="props.item.image"></td>
          <td class="text-xs-center">{{ props.item.letter}}</td>
          <td class="justify-center layout px-2">
            <v-btn  small color="success" class="mx-0" >
              编辑
            </v-btn>
            <v-btn  small color="warning"  class="mx-0" >
              删除
            </v-btn>
          </td>
        </template>
      </v-data-table>
      <!--弹出框  scrollable 不可滚动  persistent 窗口外不可关闭-->
      <v-dialog v-model="show" max-width="500"   scrollable  persistent>
        <!-- 卡片-->
          <v-card>
            <!--dense 高度-->
            <v-toolbar dense color="primary" dark  class="subheading  px-2 " >
              <span>新建品牌</span>
              <!--填充空间-->
              <v-spacer/>
              <v-btn icon @click="show=false">
                <v-icon>
                  close
                </v-icon>
              </v-btn>

            </v-toolbar>
            <v-card-text style="height: 600px">
              <!-- 表单-->
                <MyBrandFrom/>
            </v-card-text>
          </v-card>
      </v-dialog>
    </v-card>
</template>

<script>

  import MyBrandFrom from './MyBrandFrom'
    export default {
        name: 'MyBrand',
        data(){
          return{
            headers:[{
              text: 'id',
              value: 'id',
              align:  'center',
              sortable: true
            },{
              text: '名称',
              value: 'name',
              align:  'center',
              sortable: false
            },{
              text: 'LOGO',
              value: 'image',
              align:  'center',
              sortable: false
            },{
              text: '首字母',
              value: 'letter',
              align:  'center',
              sortable: false
            },{ text: '操作',align:'center',sortable: false }],
            //  表中的数据
            brands:[],
            //  分页
            pagination:{},
            totalBrands:0,
            //  进度条
            loading:false,
            search:'',
            show:false
          }
          //  监视
        },
      created(){
          this.getDataFromServer();
      },
      watch:{
        pagination:{
          deep:true,
          handler(){
            this.getDataFromServer();
          }
        },
        search(){
          this.getDataFromServer();
        }
      },
      methods:{
          //  获取数据
        getDataFromServer(){
            // 开启加载 进度条
          this.loading=true;

          //  发送 ajax请求
          this.$http.get('/item/brand/page',{
            params:{
              key: this.search, // 搜索条件
              page: this.pagination.page,// 当前页
              rows: this.pagination.rowsPerPage,// 每页大小
              sortBy: this.pagination.sortBy,// 排序字段
              desc: this.pagination.descending// 是否降序
            }
          }).then(resp=>{
            console.log(resp.data)
            this.brands=resp.data.items;
            this.totalBrands=resp.data.total;
            //  请求 成功
            //  关闭加载进度条
            this.loading=false;
            //  窗口显示

          })

        }
      },components:{
          MyBrandFrom
      }
    }
</script>

<style scoped>

</style>
