<template>
  <div>
    <h1>App根组件</h1>
    <hr>
    <my-table :data="goodslist">
      <template #header>
        <th>序号</th>
        <th>商品名称</th>
        <th>价格</th>
        <th>标签</th>
        <th>操作</th>
      </template>
      <template #body="{row,index}">
        <td>{{index+1}}</td>
        <td>{{row.goods_name}}</td>
        <td>￥{{row.goods_price}}</td>
        <td>
          <!-- 基于当前行的 inputVisible，来控制 input 和 button 的按需展示-->
          <input type="text" class="form-control form-control-sm ipt-tag"
          v-if="row.inputVisible" v-focus v-model.trim="row.inputValue" @blur="onInputConfirm(row)"  @keyup.enter="onInputConfirm(row)" @keyup.esc="row.inputValue=''">
          <button type="button" class="btn btn-primary btn-sm" v-else @click="row.inputVisible = true">+Tag</button>
          <span class="badge badge-warning ml-2" 
          v-for="item in row.tags" :key="item">{{item}}</span>
        </td>
        <td>
          <button type="button" class="btn btn-danger btn-sm" @click="onRemove(row.id)">删除</button>
        </td>
      </template>
    </my-table>
  </div>
</template>

<script>
import MyTable from './components/my-table/MyTable.vue'
export default {
  name:'MyApp',
  data() {
    return {
      // 商品列表的数据
      goodslist:[]
    }
  },
  created(){
    // 发起请求
    this.getGoodsList()
  },
  methods:{
    async  getGoodsList(){
      const {data:res} =await this.$http.get('/api/goods')
      // console.log(res);
      if(res.status!==0) return console.log('获取商品列表失败');
      this.goodslist=res.data
      },
      // 根据id删除商品
      onRemove(id){
        this.goodslist = this.goodslist.filter(x=>x.id!==id)
      },
      // 文本框失去焦点 接收数据
      onInputConfirm(row){
        const val= row.inputValue
        console.log(val);
        row.inputValue=''
        row.inputVisible=false
        if(!val || row.tags.indexOf(val)!==-1) return
        row.tags.push(val)
      }
    },
  components:{
    MyTable
  }
}
</script>

<style lang='less' scoped>
  .ipt-tag {
    width: 80px;
    display: inline;
  }
</style>