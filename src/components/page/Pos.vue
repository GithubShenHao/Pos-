<template>
  <div class="pos">
    <el-row>
      <el-col :span='7' class='pos-order' id="order-list">
        <el-tabs>
          <el-tab-pane label='点餐'>
          <el-table :data='tableData' border style="width:100%">
          <el-table-column prop='goodsName' label='商品名称'></el-table-column>
          <el-table-column prop='count' label='数量' width='50'></el-table-column>
          <el-table-column prop='price' label='金额' width='70'></el-table-column>
          <el-table-column label='操作' width='100' fixed='right'>
            <template scope='scope'>
              <el-button type='text' size='small' @click="delSingleGoods(scope.row)">删除</el-button>
              <el-button type='text' size='small' @click="addOrderList(scope.row)">增加</el-button>
            </template>
          </el-table-column>
          </el-table>
          <!-- 计算区 -->
          <div class="totalDiv">
            <small>数量:</small> {{totalCount}} &nbsp;&nbsp;&nbsp;&nbsp; <small>金额:</small> {{totalMoney}}元
          </div>
          <!-- 按钮区 -->
          <div class="div-btn">
            <el-button type='warning' size='small'>挂单</el-button>          
            <el-button type='danger' size='small' @click="delAllGoods">删除</el-button>
            <el-button type='success' size='small' @click="pay">结账</el-button>
          </div>
        </el-tab-pane> 
        <el-tab-pane label='挂单'>
        </el-tab-pane> 
        <el-tab-pane label='外卖'>
        </el-tab-pane> 
        </el-tabs>
      </el-col>
      <el-col :span='17'>
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for='goods in oftenGoods' :key='goods.goodsId' @click="addOrderList(goods)">
                <span>{{goods.goodsName}}</span>
                <span class="o-price">￥{{goods.price}}元</span>
              </li>
            </ul>
          </div>
        </div>

        <div class="goods-type">
          <el-tabs>
          <el-tab-pane label='汉堡'>
              <ul class="cookList">
                <li v-for="goods in type0Goods" :key='goods.goodsId' @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
          </el-tab-pane>
          <el-tab-pane label='小食'>
            <ul class="cookList">
                <li v-for="goods in type1Goods" :key='goods.goodsId' @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
          </el-tab-pane>
          <el-tab-pane label='饮料'>
            <ul class="cookList">
                <li v-for="goods in type2Goods" :key='goods.goodsId' @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
          </el-tab-pane>
          <el-tab-pane label='套餐'>
            <ul class="cookList">
                <li v-for="goods in type3Goods" :key='goods.goodsId' @click="addOrderList(goods)">
                  <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                  <span class="foodName">{{goods.goodsName}}</span>
                  <span class="foodPrice">￥{{goods.price}}元</span>
                </li>
              </ul>
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
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalCount: 0,
      totalMoney: 0
    };
  },
  methods: {
    //添加商品
    addOrderList(goods) {
      this.totalCount = 0;
      this.totalMoney = 0;

      //判断点击的商品是否存在,如果存在数量+1,否则加入商品
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      //根据商品有无书写业务逻辑
      if (isHave) {
        //存在就进行数量添加
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        console.log(arr);
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
      this.getResult();
    },
    // 删除单个商品
    delSingleGoods(goods) {
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      this.getResult();
    },
    // 删除全部商品
    delAllGoods() {
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
    },
    // 计算数量和金额
    getResult() {
      this.totalCount = 0;
      this.totalMoney = 0;
      this.tableData.forEach(e => {
        this.totalCount += e.count;
        this.totalMoney = this.totalMoney + e.count * e.price;
      });
    },
    // 模拟结账
    pay() {
      if (this.totalCount != 0) {
        this.tableData = [];
        this.totalCount = 0;
        this.totalMoney = 0;
        this.$message({
          message: "结账成功,欢迎下次光临!",
          type: "success"
        });
      } else {
        this.$message.error("老板请点餐后结账哟!");
      }
    }
  },
  created() {
    axios
      .get(
        "https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods"
      )
      .then(res => {
        console.log(res);
        this.oftenGoods = res.data;
      })
      .catch(error => {
        console.log(error);
      });

    axios
      .get(
        "https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods"
      )
      .then(res => {
        console.log(res);
        this.type0Goods = res.data[0];
        this.type1Goods = res.data[1];
        this.type2Goods = res.data[2];
        this.type3Goods = res.data[3];
      })
      .catch(error => {
        console.log(error);
      });
  },
  mounted() {
    let orderHeight = document.body.clientHeight;
    document.getElementById("order-list").style.height = orderHeight + "px";
  }
};
</script>

<style scoped>
li {
  list-style: none;
  cursor: pointer;
}
.pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  padding: 10px;
  text-align: left;
  background-color: #f9fafc;
  border-bottom: 1px solid #d3dce6;
}
.often-goods-list ul li {
  border: 1px solid #e5e9f2;
  float: left;
  padding: 10px;
  margin: 10px;
  background-color: #fff;
}
.o-price {
  color: #58b7ff;
}
.goods-type {
  clear: both;
}
.cookList li {
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auto;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 16px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.goods-type {
  border-top: 1px solid #d3dce6;
}
.totalDiv {
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #d3dce6;
}
</style>
