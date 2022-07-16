<template>
 <div class="body-wrapper">
   <div class="body">
     <img class="video" src="/static/images/鱼.jpg" />
     <div class="data-wrapper"> 
       <div class="title">功能</div>
      <div class="data">
         <img class="data-logo" src="/static/images/温度.png" />
         <div class="data-text">
           <div class="data-title">温度监测</div>
           <div class="data-description">27.2℃</div>
         </div>
       </div>
      <div class="data">
         <img class="data-logo" src="/static/images/水位.png" />
         <div class="data-text">
           <div class="data-title">水位监测</div>
           <div class="data-description">7.53cm</div>
         </div>
       </div>
      <div class="data">
         <img class="data-logo" src="/static/images/水质浊度.png" />
         <div class="data-text">
           <div class="data-title">水质浊度</div>
           <div class="data-description">{{Turbidity?'清澈':'清澈'}}</div>
         </div>
       </div>
       <div class="data">
         <img class="data-logo" src="/static/images/喂食.png" />
         <div class="data-text">
           <div class="data-title">自动喂食</div>
           <div class="data-description">开启</div>
         </div>
       </div>
       <div class="data">
         <img class="data-logo" src="/static/images/灯光.png" />
         <div class="data-text">
           <div class="data-title">灯光设置</div>
             <switch  @change="onLedChange" :checked="Led" color="#3E82FF" />
         </div>
       </div>
       <div class="data">
         <img class="data-logo" src="/static/images/健康监测.png" />
         <div class="data-text">
           <div class="data-title">健康监测</div>
             <switch color="#3E82FF" />
         </div>
       </div>
       <div>
       <button class="button" @click=details()>查看详情</button>
       </div>
     </div> 
   </div>
 </div>
</template>


<script>
// import { connect } from "mqtt"
import { connect } from "mqtt/dist/mqtt.js";
// const mqttHost = "mqtt.smartfishhome.fun"; //mqtt 服务器域名/IP
const mqttHost = "120.25.125.107"; //mqtt 服务器域名/IP
const mqttPort = 8084; //mqtt 服务器域名/IP
const mqttUrl = `wxs://${mqttHost}:${mqttPort}/mqtt`; //  mqtt连接路径

export default {
  data () {
    return {
      client: {},
      Temp:0,
      Water_level:0,
      Turbidity:0,
      Led:false,
      Health:false
    };
  },
  components: {},
  methods: {
     onLedChange(event){
      var that = this
      console.log(event.mp.detail)
      let sw = event.mp.detail.value
      if(sw){
        that.client.publish("/mysmarthome/sub",'{"target":"LIGHT","value":"11"}',function(err){
          if(!err){
            console.log("成功下发命令——开灯")
          }
        })
      }else{
          that.client.publish("/mysmarthome/sub",'{"target":"LIGHT","value":"00"}',function(err){
          if(!err){
            console.log("成功下发命令——关灯")
          }
        })
      }
    },

     details(){
       wx.navigateTo({
       url: '/pages/details/main'})
  },},

  onShow(){
    var that = this;
    that.client = connect(mqttUrl);
    that.client.on("connect", function () {
      console.log("成功连接mqtt服务器!");
      that.client.subscribe("/mysmarthome/pub", function (err) {
           if (!err) {
            console.log("成功订阅设备上行数据Topic!");
           }
      })
    })
      that.client.on("message", function (topic, message) {
        console.log(topic);
      // message是16进制的Buffer字节流
        let dataFromDev = {};
        dataFromDev = JSON.parse(message);
        console.log(dataFromDev);
        that.Temp = dataFromDev.Temp;
        that.Turbidity = dataFromDev.Turbidity;
        that.Water_level = dataFromDev.Water_level;
   
    });
  },
};
</script>

<style lang="scss" scoped>
.body-wrapper{
  background-color: #f7f7f7;
  .video{
    width: 500px;
    height: 240px;
  }
  .data-wrapper{
   padding: 20px; 
   display: flex;
   flex-direction: column;
   justify-content: space-between;
   .title{
     color: #3d3d3d;
     font-size: 20px;
     font-weight: bold;
     margin-bottom: 5px;
   }
   .data{
     background-color: #fff;
     margin-top: 15px;
     width: 320px;
     height: 90px;
     border-radius: 20px;
     display: flex;
     padding: 0 8px;
     box-shadow: #d6d6d6 0px 0px 5px;
     .data-logo{
       height: 60px;
       width: 60px;
       margin-top: 15px;
       margin-left: 5px;
     }
     .data-text{
       margin-top: 25px;
       width: 240px;
       height: 90px;
       color: #3d3d3d;
       display: flex;
       justify-content: space-between;
       margin-left: 15px;
       .data-title{
         font-size: 18px;
         font-weight: bold;
         margin-top: 6px;
       }
       .data-description{
         color: #3e82ff;
         font-size: 28px;

         margin-inline-end: 10px;
       }
     }
   }
    .button{
    margin-top: 20px;
    background-color: #3e82ff;
    border-radius: 20px;
    color: #fff;
    padding: 6px 135px;
    text-align: center;
    text-decoration: none;
    display: flex;
    font-weight: bold;
    font-size: 16px;
    box-shadow: #d6d6d6 0px 0px 5px;
  }
  }
}
</style>

