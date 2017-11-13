<template>
  <div class="pos">
    <div class="pos-order">
	    <el-tabs>
	      <el-tab-pane label="点餐">
	      	<el-table :data="tableData" border style="width: 100%" >
	      	    <el-table-column prop="goodsName" label="商品" class='Orderitem'>
	      	    	
	      	    </el-table-column>
	      	    <el-table-column prop="count" label="量">
	      	    	
	      	    </el-table-column>
	      	    <el-table-column prop="price" label="金额" class='Orderitem'>
	      	    	
	      	    </el-table-column>
	      	    <el-table-column  label="操作" fixed="right" class='Orderitem'>
	      	        <template slot-scope="scope">
	      	            <el-button type="text" size="small" @click='deletSingleGoods(scope.row)'>删除</el-button>
	      	            <el-button type="text" size="small" @click='addOrderList(scope.row)'>增加</el-button>      	 
	      	        </template>
	      	    </el-table-column>
	      	</el-table>   
	      	<div class="totalDiv">
	      		<small>数量：</small>{{totalCount}}
	      		&nbsp;&nbsp;&nbsp;&nbsp;
	      		<small>金额：</small>{{totalMoney}}元
	      	</div>
	      	<div class="div-btn">
	      		<el-button type="warning" >挂单</el-button>
	      		<el-button type="danger" @click='deletAllGoods()'>删除</el-button>
	      		<el-button type="success" @click='checkout'>结账</el-button>
	      	</div>
	      </el-tab-pane>
	      <el-tab-pane label="挂单">
	      挂单
	      </el-tab-pane>
	      <el-tab-pane label="外卖">
	      外卖
	     </el-tab-pane>
		</el-tabs>
    </div>
    <div class="pos-main">
    	<div class="often-goods">
    		<div class="title">常用商品</div>
    		    <div class="often-goods-list">
    		        <ul>
    		            <li v-for='(item,index) in oftenGoods' @click='addOrderList(item)'>
    		                <span>{{item.goodsName}}</span>
    		                <span class="o-price">￥{{item.price}}元</span>
    		            </li>
    		 
    		        </ul>
    		    </div>
    	</div>
    	<div class="goods-type">   	 
    	    <el-tabs>
    	        <el-tab-pane label="汉堡">
    	            <ul class='cookList'>
    	                <li v-for='(item,index) in type0Goods' @click='addOrderList(item)'>
    	                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
    	                    <span class="foodName">{{item.goodsName}}</span>
    	                    <span class="foodPrice">￥{{item.price}}元</span>
    	                </li>
    	            </ul>
    	        </el-tab-pane>
    	            <el-tab-pane label="小食">
    	            <ul class='cookList'>
    	                <li v-for='(item,index) in type1Goods' @click='addOrderList(item)'>
    	                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
    	                    <span class="foodName">{{item.goodsName}}</span>
    	                    <span class="foodPrice">￥{{item.price}}元</span>
    	                </li>
    	            </ul>
    	        </el-tab-pane>
    	        <el-tab-pane label="饮料">
    	            <ul class='cookList'>
    	                <li v-for='(item,index) in type2Goods' @click='addOrderList(item)'>
    	                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
    	                    <span class="foodName">{{item.goodsName}}</span>
    	                    <span class="foodPrice">￥{{item.price}}元</span>
    	                </li>
    	            </ul>
    	        </el-tab-pane>
    	        <el-tab-pane label="套餐">
    	            <ul class='cookList'>
    	                <li v-for='(item,index) in type3Goods' @click='addOrderList(item)'>
    	                    <span class="foodImg"><img :src="item.goodsImg" width="100%"></span>
    	                    <span class="foodName">{{item.goodsName}}</span>
    	                    <span class="foodPrice">￥{{item.price}}元</span>
    	                </li>
    	            </ul>
    	        </el-tab-pane>  	 
    	    </el-tabs>
    	</div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'pos',
  created(){
        axios.get('http://jspang.com/DemoApi/oftenGoods.php')
        .then(response=>{
           console.log(response);
           this.oftenGoods=response.data;
        })
        .catch(error=>{
            console.log(error);
            alert('网络错误，不能访问');
        })
        axios.get('http://jspang.com/DemoApi/typeGoods.php')
              .then(response=>{
                 console.log(response);
                 this.type0Goods=response.data[0];
                 this.type1Goods=response.data[1];
                 this.type2Goods=response.data[2];
                 this.type3Goods=response.data[3];
              })
              .catch(error=>{
                  console.log(error);
                  alert('网络错误，不能访问');
              })
    },
  data () {
    return {
      tableData: [],
      oftenGoods:[],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
      totalMoney:0,
      totalCount:0
    }
  },
  methods:{
  	addOrderList(goods){
  		//商品是否存在于订单列表中
  		let isHave = false;
  		if(this.tableData.find((n) => n.goodsId == goods.goodsId)!= undefined){
  			isHave = true;
  		}
  		//根据判断
  		if (isHave) {
  			let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
  			arr[0].count++;
  		}else{
  			let newGoods = {
  				goodsId:goods.goodsId,
  				goodsName:goods.goodsName,
  				price:goods.price,
  				count:1
  			};
  			this.tableData.push(newGoods);
		}
		this.getAll();
  	},
  	//删除单个商品
  	deletSingleGoods(goods){
  		this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
  		this.getAll();
  	},
  	//删除所有商品
  	deletAllGoods(){
  		this.tableData = [];
  		this.totalCount =0;
  		this.totalMoney =0;
  	},
  	//汇总数量和总金额
  	getAll(){
  		this.totalCount =0;
  		this.totalMoney =0;
  		if (this.tableData) {
  			this.tableData.forEach((item)=>{
  				this.totalCount += item.count;
  				this.totalMoney += item.count * item.price;
  			})  
  		}
  	},
  	//结账
  	checkout() {
  	    if (this.totalCount!=0) {
  	        this.tableData = [];
  	        this.totalCount = 0;
  	        this.totalMoney = 0;
  	        this.$message({
  	            message: '结账成功，感谢你又为店里出了一份力!',
  	            type: 'success'
  	        });
  	 
  	    }else{
  	        this.$message.error('不能空结。老板了解你急切的心情！');
  	    }
  	 
  	}
  }
}
</script>


<style scoped>
	.pos-order{
		width: 30vw;
		height: 100vh;
		background-color: #F9FAFC;
		border-right: 1px solid #C0CCDA;
		float: left;
	}
	.div-btn{
		margin-top: 1vw;
	}
	.pos-main{
		width: 62vw;
		float: left;
	}
	.title{
        height: 19px;
        border-bottom:1px solid #D3DCE6;
        background-color: #F9FAFC;
        padding:10px;
    }
    .often-goods-list ul li{
        list-style: none;
        float:left;
        border:1px solid #E5E9F2;
        padding:10px;
        margin:5px;
        background-color:#fff;
    }
    .o-price{
        color:#58B7FF; 
    }
    .goods-type{
    	clear: both;
    	margin-top: 3vw;
    	margin-left: 3vw;
    }
    .cookList li{
           list-style: none;
           width:23%;
           border:1px solid #E5E9F2;
           height: auot;
           overflow: hidden;
           background-color:#fff;
           padding: 2px;
           float:left;
           margin: 2px;
           cursor: pointer;
     
       }
    .cookList li span{
       
        display: block;
        float:left;
    }
    .foodImg{
       width: 40%;
    }
    .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;
 
    }
    .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
    }
    .totalDiv{
    	background-color: #fff;
    	padding: 10px;
    	border-bottom: 1px solid #D3DCE6;
    }
</style>
