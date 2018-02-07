<template>
  <div class="pos">
		<el-row>
			<el-col :span="7" class="posOrder" id="orderList">
				<el-tabs>
					<el-tab-pane label="点餐">
						<el-table :data="tableData" border style="style:100%;">
							<el-table-column prop="goodsName" label="商品名称"></el-table-column>
							<el-table-column prop="count" label="量" width="50"></el-table-column>
							<el-table-column prop="price" label="金额" width="70"></el-table-column>
							<el-table-column label="操作" width="100" fixed="right">
								<template scope="scope">
									<el-button type="text" size="small" @click="delSingGoods(scope.row)">删除</el-button>
									<!--订单列表中绑定是需要特殊处理一下的，需要用到template的scope值，进行绑定。-->
									<el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
								</template>
							</el-table-column>			
						</el-table>
						<div class="totalDiv">
							<small>数量：</small>{{totalCount}}  &nbsp; <small>金额：</small>{{totalMoney}}元
						</div>
						<div class="divBtn">
							<el-button type="warning">挂单</el-button>
							<el-button type="danger" @click="delAllGoods()">删除</el-button>
							<el-button type="success" @click="checkout()">结账</el-button>
						</div>
					</el-tab-pane>
					<el-tab-pane label="挂单">
						挂单内容
					</el-tab-pane>
					<el-tab-pane label="外卖">
						外卖内容
					</el-tab-pane>
				</el-tabs>	
				
			</el-col>
			 <!--商品展示-->
			<el-col :span="17">
				<div class="oftenGoods">
					<div class="title">常用商品</div>
					<div class="oftenGoodsList">
						<ul>
							<li v-for="goods in oftenGoods" @click="addOrderList(goods)">
								<span>{{goods.goodsName}}</span>
								<span class="oPrice">￥{{goods.price}}</span>
							</li>
						</ul>
					</div>
				</div>
				
				<div class="goodsType">
					<el-tabs>
						<el-tab-pane label="汉堡">
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
import axios from 'axios'
export default {
  name: 'Pos',
  data(){
  	return {
  		tableData:[
//			{
//        goodsName: '可口可乐',
//        price: 8,
//        count:1
//      }, {
//        
//        goodsName: '香辣鸡腿堡',
//        price: 15,
//        count:1
//      }, {
//       
//        goodsName: '爱心薯条',
//        price: 8,
//        count:1
//      }, {
//       
//        goodsName: '甜筒',
//        price: 8,
//        count:1
//    }
      ],
      oftenGoods:[],
      type0Goods:[],
			type1Goods:[],
			type2Goods:[],
			type3Goods:[],
			totalMoney:0,
			totalCount:0
  	}
  },
  created(){
  	//常用商品
		axios.get('http://jspang.com/DemoApi/oftenGoods.php')
		.then(response=>{
			console.log(response);
			this.oftenGoods=response.data;
		})
		.catch(error=>{
			console.log(error);
			alert('网络错误，不能访问');
		})
		//分类商品
		axios.get('http://jspang.com/DemoApi/typeGoods.php')
		.then(response=>{
			console.log(response);
				this.type0Goods=response.data[0];
				this.type1Goods=response.data[1];
				this.type2Goods=response.data[2];
				this.type3Goods=response.data[3];
		})
		.catch(error=>{
			alert('网络错误，不能访问');
		})
  },
 	mounted:function(){
 		var orderHeight=document.body.clientHeight;
 		//console.log(orderHeight);
		document.getElementById("orderList").style.height=orderHeight+"px";
 	},
 	methods:{
 		addOrderList(goods){
 			this.totalMoney=0;
 			this.totalCount=0;
 			
 			let isHave=false;
 			//判断是否这个商品已经存在于订单列表
 			for(let i=0;i<this.tableData.length;i++){
 				//console.log(this.tableData[i].goodsId);
 				if(this.tableData[i].goodsId==goods.goodsId){
 					isHave=true;//存在
 				}
 			}
 			//根据isHave的值判断订单列表中是否已经有此商品	
 			if(isHave){
 				//存在就进行数量添加
 				let arr=this.tableData.filter(o =>o.goodsId == goods.goodsId);
				console.log(arr);
 				arr[0].count++;
 			}else{
 				//不存在就推入数组
 				let newGoods={goodsId:goods.goodsId,goodsName:goods.goodsName,price:goods.price,count:1};
 				this.tableData.push(newGoods);
 			}
 			this.getAllMoney();
 		},
 		//删除单个商品
 		delSingGoods(goods){
 			this.tableData=this.tableData.filter(o => o.goodsId != goods.goodsId);
 			this.getAllMoney()
 		},
 		//删除所有商品
 		delAllGoods(){
 			var istrue=confirm("您确定要删除该订单吗"); 
 			if(istrue){
	 			this.tableData=[];
	 			this.totalMoney=0;
	 			this.totalCount=0;
 			}
 		},
 		//模拟结账
 		checkout(){
			if(this.totalCount!=0){
				this.tableData=[];
	 			this.totalMoney=0;
	 			this.totalCount=0;
	 			//elementui提供的用法。
	 			this.$message({
	 				message:'结账成功，感谢你又为店里出了一份力！',
	 				type:'success'
	 			});
			}else{
					this.$message.error('不能空结。老板了解你急切的心情！');
			}
 		},
 		//汇总数量和金额
 		getAllMoney(){
 			this.totalCount=0;
 			this.totalMoney=0;
 			if(this.tableData){
	 			//进行数量和价格的汇总计算
	 			this.tableData.forEach((element) => {
	 				this.totalCount+=element.count;
	 				this.totalMoney+=element.price*element.count;
	 			});
 			}
 		}
 	}
}
</script>
<style scoped>
.posOrder{
	background:#f9fafc;
	border-right:1px solid #000000;
}
.divBtn{
	margin-top: 10px;
}
.oftenGoods .title{
	height: 20px;
  border-bottom:1px solid #D3DCE6;
  background-color: #F9FAFC;
  padding:10px;
  text-align: left;
}
.oftenGoodsList ul li{
	list-style: none;
	float:left;
	border:1px solid #E5E9F2;
	padding:10px;
	margin:5px;
	background-color:#fff;
	cursor: pointer;
}
.oPrice{
	color:#58B7FF; 
}
.goodsType{
	clear:both;
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
   cursor:pointer;
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
	padding:10px;
	background:#FFFFFF;
	border-bottom: 1px solid #E5E9F2;
}
</style>