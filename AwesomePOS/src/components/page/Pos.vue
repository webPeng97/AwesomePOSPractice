<template>
  <div class="pos">
    <el-row>
      <!-- 订单栏 -->
      <el-col :span="7" class="pos-order" id="order-list">        
        <el-tabs>
          <el-tab-pane label="点餐">
           <el-table :data="tableData" border style="width: 100%" > 
                <el-table-column prop="goodsName" label="商品"  ></el-table-column>
                <el-table-column prop="count" label="量" width="50"></el-table-column>
                <el-table-column prop="price" label="金额" width="70"></el-table-column>
                <el-table-column  label="操作" width="100" fixed="right">
                    <template scope="scope">
                        <el-button type="text" size="small" @click="delSingGoods(scope.row)">删除</el-button>
                        <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>            
                    </template>
                </el-table-column>
            </el-table>
            <div class="totalDiv">
              <small>数量：</small>{{totalCount}}   <small>金额：</small>{{totalMoney}}元
            </div>
            <div class="div-btn">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="delAllGoods()">删除</el-button>
              <el-button type="success" @click="checkout()">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">
            挂单
          </el-tab-pane>
          <el-tab-pane label="外卖">
            外卖
          </el-tab-pane>
        </el-tabs>
      </el-col>
      <!-- 产品栏 -->
      <el-col :span="17">       
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}</span>
              </li>
            </ul>
          </div>
        </div> 
        
        <div class="goods-type">
            <el-tabs>
              <el-tab-pane  label="汉堡">
                <div>
                  <ul class="cookList">
                    <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
              <el-tab-pane label="小食">
                 <div>
                  <ul class="cookList">
                    <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
              <el-tab-pane label="饮料">
                 <div>
                  <ul class="cookList">
                    <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
              <el-tab-pane label="套餐">
                 <div>
                  <ul class="cookList">
                    <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
            </el-tabs>     
        </div>      
     
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "pos",
  data() {
    return {
      tableData: [
        // {
        //   goodsName: '可口可乐',
        //   price: 8,
        //   count:1
        // }, {
        //   goodsName: '香辣鸡腿堡',
        //   price: 15,
        //   count:1
        // }, {
        //   goodsName: '爱心薯条',
        //   price: 8,
        //   count:1
        // }, {
        //   goodsName: '甜筒',
        //   price: 8,
        //   count:1
        // }
      ],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalMoney: 0,
      totalCount: 0
    };
  },
  created: function() {
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(reponse => {
        console.log(reponse);
        this.oftenGoods = reponse.data;
      })
      .catch(error => {
        console.log(error);
        alert("网络错误，不能访问");
      });
    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(reponse => {
        console.log(reponse);
        this.type0Goods = reponse.data[0];
        this.type1Goods = reponse.data[1];
        this.type2Goods = reponse.data[2];
        this.type3Goods = reponse.data[3];
      })
      .catch(error => {
        console.log(error);
        alert("网络错误，不能访问");
      });
  },
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    console.log(orderHeight);
    document.getElementById("order-list").style.height = orderHeight + "px";
  },
  methods: {
    addOrderList(goods) {
      this.totalMoney = 0;
      this.totalCount = 0;

      //商品是否已存在点单列表中
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      //根据判断的值编写业务逻辑
      if (isHave) {
        //改变列表中商品的数量
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
      this.getAllMoney();
    },
    //删除单个商品
    delSingGoods(goods) {
      console.log(goods);
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      this.getAllMoney();
    },
    //删除所以商品
    delAllGoods(){
      this.totalMoney=0;
      this.totalCount=0;
      this.tableData=[];
    },
    //结账
    checkout(){
      if(this.totalCount!=0){
         this.totalMoney=0;
        this.totalCount=0;
        this.tableData=[];
        this.$message({
          message:'结账成功',
          type:'success'
        });
      }else{
        this.$message.error('商品为空');
      }
    },
    //汇总数量和金额
    getAllMoney() {
      this.totalCount = 0;
      this.totalMoney = 0;
      if (this.tableData) {
        //计算金额和数量
        this.tableData.forEach(ele => {
          this.totalCount += ele.count;
          this.totalMoney = this.totalMoney + ele.price * ele.count;
        });
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often-goods-list ul li {
  float: left;
  border: 1px solid #e5e9f2;
  padding: 10px;
  margin: 10px;
  background-color: #fff;
  box-shadow: 0 0 5px #d3dce6;
  cursor: pointer;
}
.o-price {
  color: #58b7ff;
}
.goods-type {
  clear: both;
}
.cookList {
  padding: 10px 20px;
}
.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 18px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
</style>
