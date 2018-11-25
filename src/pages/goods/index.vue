<template>
    <div class="goods-list-container">
        <!-- <router-link class="goods-item" v-for="item in goodslist" :key="item.id" :to="'/home/goodsInfo/'+item.id">
            <img src="https://img10.360buyimg.com/n7/jfs/t24934/126/1102620868/432389/ea09eed9/5b8a5c38N64121d0b.png" alt="">
            <h1 class="title">{{item.title}}</h1>
            <div class="info">
                <p class="price">
                    <span class="now">￥{{item.sell_price}}</span>
                    <span class="old">￥{{item.market_price}}</span>
                </p>
                <p class="sell">
                    <span>热卖中</span>
                    <span>剩{{item.stock_quantity}}件</span>
                </p>
            </div>
        </router-link> -->



            <div class="goods-item" v-for="item in goodslist" :key="item.id" @click="goDetail(item.id)">
            <img src="https://img10.360buyimg.com/n7/jfs/t24934/126/1102620868/432389/ea09eed9/5b8a5c38N64121d0b.png" alt="">
            <h1 class="title">{{item.title}}</h1>
            <div class="info">
                <p class="price">
                    <span class="now">￥{{item.sell_price}}</span>
                    <span class="old">￥{{item.market_price}}</span>
                </p>
                <p class="sell">
                    <span>热卖中</span>
                    <span>剩{{item.stock_quantity}}件</span>
                </p>
            </div>
        </div>

        <mt-button type="danger" size="large" @click="gitMore">加载更多</mt-button>
    </div> 
</template>

<script>
export default {
    data(){
        return{
          pageindex:1, //分页数
          goodslist:[],
        }
    },
    created(){
        this.getgoodslist();
    },
    methods:{
        getgoodslist(){
            this.$http.get('api/getgoods?pageindex='+this.pageindex).then(result=>{
               if(result.body.status===0){
                //    this.goodslist=result.body.message;
                this.goodslist=this.goodslist.concat(result.body.message);
               } 
            });
        },
        gitMore(){
        this.pageindex++
        this.getgoodslist();
        },
        goDetail(id){
            //使用JS的形式进行路由导航
            //其中this.$route是路由参数对象所有路由中的参数
            // this.$router是一个路由导航对象
            // console.log(this);
            // 1.最简单的
            // this.$router.push("/home/goodsInfo/" +id);
            // 2.传递对象
            // this.$router.push({path:"/home/goodsInfo/" +id})
            // 3.传递一个命名的路由
            this.$router.push({ name:"goodsinfo",params:{id}})

        }
    }   
    
}
</script>

<style lang="less">
    .goods-list-container{
        display: flex;
        flex-wrap: wrap;
        padding: 7px;
        justify-content: space-between;
        .goods-item{
            width: 49%;
            border: 1px solid #ccc;
            box-shadow: 0 0 8px #ccc;
            margin: 4px 0;
            padding: 2px;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            min-height: 293px;
            img{
                width: 100%;
            }
            .title{
                font-size: 14px;
            }
            .info{
                background-color: #eee;
                p{
                    margin: 0;
                    padding: 5px;
                }
                .now{
                    color: red;
                    font-size: 18px;
                    font-weight: bold;
                }
                .old{
                    text-decoration: line-through;
                    font-size: 12px;
                    margin-left: 10px;
                }
            }
            .sell{
                display: flex;
                justify-content: space-between;
                font-size: 12px;
            }
        }
    }
</style>
