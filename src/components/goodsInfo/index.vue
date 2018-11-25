<template>
    <div class="goodsInfo-container">
       <transition
            @before-enter="beforeEnter"
           @enter="enter"
           @after-enter="afterEnter" name="suc">
           
            <div class="ball" v-show="ballFlag" ref="ball"></div>
       </transition>
        <!-- 商品轮播图区域 -->
        <div class="mui-card">
		    <div class="mui-card-content">
		    	<div class="mui-card-content-inner">
		    		<swipe :bannerList="lunbotu" :isfull="false"></swipe>
		    	</div>
		    </div>
        </div>
        <!-- 商品购买区域 -->
        <div class="mui-card">
				<div class="mui-card-header">{{goodsinfo.title}}</div>
				<div class="mui-card-content">
					<div class="mui-card-content-inner">
						<p class="price">
                            市场价:<del>￥{{goodsinfo.market_price}}</del>&nbsp;&nbsp;销售价:<span class="now-price">￥{{goodsinfo.sell_price}}</span>
                        </p>
                        <p>购买数量:<numbox @getcount="getSelectedCount" :max="goodsinfo.stock_quantity"></numbox></p>
                        <p>
                            <mt-button type="primary" size="small">立即购买</mt-button>
                            <mt-button type="danger" size="small" @click="addToshopCar">加入购物车</mt-button>
                        </p>
					</div>
				</div>
			</div>

        <!-- 商品参数区域 -->
        <div class="mui-card">
				<div class="mui-card-header">商品参数</div>
				<div class="mui-card-content">
					<div class="mui-card-content-inner">
						<p>商品货号:{{goodsinfo.goods_no}}</p>
                        <p>库存情况:{{goodsinfo.stock_quantity}}件</p>
                        <p>上架时间:{{goodsinfo.add_time}}</p>
					</div>
				</div>
				<div class="mui-card-footer">
                    <mt-button type="primary" size="large" plain 
                    @click="geDesc(id)">图文介绍</mt-button>
                    <mt-button type="danger" size="large" plain
                    @click="goComment(id)">商品评论</mt-button>
                </div>
			</div>
    </div>
</template>

<script>
//导入轮播图
import swipe from '../swiper/index';
//导入数字选择框
import numbox from '../goodsnumbox/index';
export default {
    data(){
        return{
            id:this.$route.params.id,
            lunbotu:[
                {img:'https://img14.360buyimg.com/n7/jfs/t13027/7/251166586/170323/69be4c91/5a071952N8a6407d6.jpg',url:'sss'},
                {img:'https://img10.360buyimg.com/n7/jfs/t4693/315/3466909188/93761/aed866cc/58fea7a5N051a15ee.jpg',url:'sss'}
            ],
            goodsinfo:[],
            ballFlag:false,//控制小园的隐藏和显示
            selectedCound:1 //保证用户选中的商品数量,默认认为用户买一个
        };
    },
    created(){
        this.getLunbotu();
        this.getGoodsInfo();
    },
    methods:{
        getLunbotu(){
            this.$http.get('api/getthumimages/'+this.id).then(result=>{
                if(result.body.status===0){
                    //先循环轮播图书证的每一项为item添加img属性因为轮播图组件中只认识item.img
                    result.body.message.forEach(item=> {
                        item.img=item.src;
                    });
                    // this.lunbotu=result.body.message;
                }
            })
        },
        getGoodsInfo(){
            //获取商品信息
            this.$http.get('api/goods/getinfo/'+this.id).then(result=>{
                if(result.body.status===0){
                    this.goodsinfo=result.body.message[0];
                }
            })
        },
        geDesc(id){
            //点击使用编程式导航跳转到图文介绍页面
            this.$router.push({name:"goodsdesc",params:{ id }})
        },
        goComment(id){
            //跳转到评论页面
             this.$router.push({name:"goodscomment",params:{ id }});
        },
        addToshopCar(){
            this.ballFlag=!this.ballFlag
        },
        beforeEnter(el){
            el.style.transform="translate(0, 0)";
        },
            enter(el,done){
                el.offsetWidth;
                //获取小球的在页面中的位置
                const ballPosition=this.$refs.ball.getBoundingClientRect();
                //获取徽标在页面中的位置
                const badgePosition=document.getElementById('badge').getBoundingClientRect();

                const xDist=badgePosition.left-ballPosition.left;
                const yDist=badgePosition.top -ballPosition.top;
                // console.log(ballPosition)
                // console.log(badgePosition)
                // console.log(ballPosition.top)
                // console.log(badgePosition.top)jhu
                // console.log(yDist)



                //  el.style.transform='translate(193px,240px)';
                el.style.transform=`translate(${xDist}px,${yDist}px)`;
                el.style.transition="all 1s cubic-bezier(.17,.67,.85,.55)";
                done()
            },
            afterEnter(el){
                this.ballFlag=!this.ballFlag;
            },
            getSelectedCount(count){
                //当子组件把选中的数量传递给父组件的时候把选中的值保存到data上
               this.selectedCound=count; 
               console.log("父组件拿到的数量值为:"+this.selectedCound);
            },
            
        

    },
    components:{
        swipe,
        numbox
    }
}
</script>

<style lang="less">
.goodsInfo-container{
    background-color: #eee;
    overflow: hidden;
}
.now-price{
    color: red;
    font-size: 18px;
    font-weight: bold;
}
.mui-card-footer{
    display: block;
    button{
        margin: 15px 0;
    }
}
.ball{
    width: 15px;
    height: 15px;
    border-radius: 50%;
    background-color: red;
    position: absolute;
    z-index: 99;
    top: 399px;
    left:137px ;

}
</style>
