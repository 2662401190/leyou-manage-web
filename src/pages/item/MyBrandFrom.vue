<template>
    <v-form v-model="valid" ref="brandForm">
      <!-- required  必填项-->
      <v-text-field v-model="brand.name" label="品牌名称" required  :rules="[v => !!v || '品牌名称不能为空']"/>
      <v-text-field v-model="brand.letter" label="品牌首字母" required mask="A"  :rules="[v => !!v || '品牌字母不能为空']"/>
      <!-- 类似于 Html中select 组件-->
      <v-cascader url="/item/category/list" multiple required  label="请选择商品分类" />
      <!-- 文件上传-->
      <v-layout row>
        <v-flex xs4  class="subheader" >品牌logo</v-flex>
        <v-flex >
          <v-upload v-model="brand.image" url="/upload/image" :multiple="false" :pic-width="250" :pic-height="90"/>
        </v-flex>
      </v-layout>
      <v-layout row class="pt-5">
        <v-spacer/>
        <v-btn color="primary" @click="submit">提交</v-btn>
        <v-btn color="warning" >重置</v-btn>
      </v-layout>
    </v-form>
</template>

<script>
    import Upload from "element-ui/packages/upload/src/upload";
    export default {
        name: "MyBrandFrom",
      components: {Upload},
      data(){
          return{
            valid:false,
            brand:{name:'',letter:'',image:'',categories:[]}
          }
      },methods:{
          submit(){
              if (this.$refs.brandForm.validate()) {
                // 结构表达式获取品牌中的数据，除了categories以外的都存到了 rest中
                const {categories,... rest}=this.brand;
                // 对categories进行处理，得到里面的id,赋值给rest中categories , join 把数组变成字符串 不然数据的展示方式不一样
                rest.categories=categories.map(c=>c.id).join(",");
                  this.$http.post('/item/brand',this.$qs.stringify(rest)
                  ).then(resp=>{
                    console.log('新增成功')
                  }).catch(()=>{
                    console.log('新增失败')
                  })
              }
          }
      }
    }
</script>

<style scoped>

</style>
