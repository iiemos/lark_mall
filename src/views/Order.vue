<template>
<div class="order">
  <Header :headerobj="headerobj"></Header>
  <div class="order_content">
    
    <template v-if="orders.length !== 0">
      
      <div class="order_list" v-for="(item,index) in orders" :key="index">
        
        <div class="order_list_header">
          <div class="time">编号：{{ item.order_id }}</div>

            <template v-if="item.status == 0">
              <div class="status">交易成功</div>
            </template>
            <!-- 虚拟实物 状态判断 ======== -->
            <!-- attr 0 实物 其他虚拟 -->
            <template v-if="item.status == 1  || item.good != null && item.good.attr == 0 ">
              <div class="status">已发货</div>
            </template>


        </div>

        <div class="order_list_info">
          <div class="list_info_left">
            <template v-if="item.good != null">
              <img :src="item.good.image[0]" />
            </template>
            <template v-else>
              <img src="../assets/images/logo.svg" alt="">
            </template>
          </div>
          <div class="list_info_right">
            <div class="right_title">
              <template v-if="item.good == null">
                商品已下架
              </template>
              <template v-else>
                {{ item.good.title }}
              </template>
            </div>
            <div class="right_pic">
              <span>{{ item.unit }} Lark</span>
              <span>x{{ item.amount }}</span>
            </div>
          </div>
        </div>
        <div class="order_list_bottom">
          <div class="pic_item">
            合计：
            <span>{{ Number(item.unit) * Number(item.amount) }} Lark</span>
          </div>
          <div class="pic_btn">
            <span v-if="item.attr == 0 && item.status == 1" class="check_order" @click="seeLog">查看物流</span>
            <span v-if="item.good != null" @click="rebay(item.good.id, item.good.status)">重新购买</span>
          </div>
          
          <van-dialog
            v-if="item.attr == 0" 
            v-model="dialog_show"
            title="物流信息"
            confirmButtonColor="#5829DB">
            <template v-if="item.status == 0">
              <div class="bullet_none">
                暂无物流信息
              </div>
            </template>
            <template v-if="item.status == 1">
              <div class="bullet_item">
                <span>物流公司</span>
                <span class="item_con firm"> 
                  {{ item.courier }}
                </span>
              </div>
              <div class="bullet_item">
                <span>物流单号</span>
                <span class="item_con single_num"> 
                  {{ item.tracking }}
                </span>
              </div>
            </template>
          </van-dialog>
        </div>
      </div>

    </template>
    
    <template v-else>
      <div class="list_null">暂无订单信息</div>
    </template>
    <Footer :isctive="1"></Footer>
  </div>
</div>

</template>

<script>
import Header from '@/components/header.vue'
import Footer from "../components/footer.vue";
import { Dialog } from 'vant';
export default {
  name: "order",
  components: {
    Footer,
    Header,
    [Dialog.Component.name]: Dialog.Component,
  },
  data() {
    return {
      dialog_show: false,
      headerobj: {
        iscross: true, // 是否显示关闭按钮还是回退按钮
        iswallet: false, // 是否显示切换钱包按钮
        text: "我的订单" // 头部文案
      },
      orders:[],
    };
  },
  mounted () {
    this.getorders()
  },
  methods: {
    getorders() {
      this.axios.get(`/orders`).then((res) => {
        if(res.status === 200) this.orders = res.data.data
      })      
    },
    rebay(id, status){
      if(status === 1){
        this.$router.push(`/details/${id}`)
      }else if( status != 1 ){
        this.$toast('库存不足，正在补货中');
      }
    },
    seeLog() {
      this.dialog_show = true;
    }
  },
};
</script>

<style lang="less" scoped>
.order_content{
  text-align: left;
  min-height: 90vh;
  padding: 15px 15px 70px 15px;
  background-color: #fff;
  & /deep/ .van-dialog__header{
    color: #34333D;
    font-weight: 400;
  }
}
 
.order_list{
  box-shadow:0px 2px 6px 0px rgba(201,197,235,0.18);
  border-radius:5px;
  border:1px solid rgba(245,245,250,1);
  padding: 14px;
  margin-bottom: 12px;
  .order_list_header{
    display: flex;
    justify-content: space-between;
    padding-bottom: 10px;
    border-bottom:1px solid rgba(245,245,250,1);
    .time{
      color: #A8A5C4;
      font-size: 14px;
    }
    .status{
      color: #5829DB;
      font-size: 14px;
    }
  }
  .order_list_info{
    padding-top: 15px;
    display: flex;
    .list_info_left{
      width: 70px;
      height: 70px;
      background:#fafafa;
      border-radius:4px;
      img{
        width: 100%;
        height: 100%;
      }
    }
    .list_info_right{
      margin-left: 15px;
      width: 73%;
      .right_title{
        color: #1C1C21;
        font-size: 14px;
        font-weight: 600;
        margin-bottom:10px;
      }
      .right_pic{
        color: #7F7C94;
        font-size: 12px;
        display: flex;
        justify-content: space-between;
      }
    }
  }
  .order_list_bottom{
    padding-top: 16px;
    .pic_item{
      color: #7F7C94;
      font-size: 12px;
      text-align: right;
      span{
        color: #FF9514;
        font-size:16px;
        padding-left: 4px;
      }
    }
    .pic_btn{
      padding-top: 14px;
      text-align: right;
      span{
        display: inline-block;
        color: #7F7C94;
        padding: 4px 6px;
        border-radius:2px;
        border:1px solid rgba(233,233,240,1);
        font-size: 14px;
        margin-left: 6px;
      }
    }
  }
}
.list_null{
  display: block;
  width: 100%;
  text-align: center;
  line-height: 80vh;
  font-size: 14px;
}
.bullet_none{
  color: #7F7C94;
  text-align: center;
  font-size: 26rem/50;
  padding: 35rem/50 38rem/50  ;
}
.bullet_item{
  display: flex;
  align-content: center;
  justify-content: space-between;
  color: #7F7C94;
  font-size: 26rem/50;
  padding: 15rem/50 38rem/50  ;
  .item_con{
    color: #34333D;
    font-size: 32rem/50;
  }
}

</style>