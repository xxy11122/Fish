<template>
<div class="body-wrapper">
  <div class="body">
    <div class="data-wrapper">

      <div class="data-temperature">
        <div class="title">
          <div class="data-title">温度监测</div>
          <div class="data-analyse">27.2℃</div>
        </div>
      <div class="data-suggestion">自动检测水温适宜，无需开启加热功能</div>
       <div class="text-wrapper">
          <div class="text">温度设置：</div>
          <input class="temperature-details" type="number" v-model="temp_l">
          <div class="text">℃ ~ </div>
          <input class="temperature-details" type="number" v-model="temp_h">
          <div class="text">℃</div>
        </div>
        <div class="temp">
          <button class="style" @click="onTempChange">更改设置</button>
        </div>
      </div>
      <div class="data">
        <div class="title">
          <div class="data-title">水位监测</div>
          <div class="data-analyse">7.53cm</div>
        </div>
      <div class="data-suggestion">自动检测水位适宜，无需开启换水功能</div>
      </div>
      <div class="data">
        <div class="title">
          <div class="data-title">水质浊度</div>
          <div class="data-analyse">{{Turbidity?'清澈':'清澈'}}</div>
        </div>
      <div class="data-suggestion">水质优质，无需开启水泵换水系统</div>
      </div>
      <div class="data-food">
        <div class="title">
          <div class="data-title">喂食设置</div>
          <div class="data-analyse">{{food_number}}g</div>
        </div>
        <div class="text-wrapper">
          <div class="text">喂食时长：</div>
          




          <input class="foos-details" type="number" v-model="food_d">
          <div class="text">d</div>
          <input class="foos-details" type="number" v-model="food_h">
          <div class="text">h</div>
          <input class="foos-details" type="number" v-model="food_m">
          <div class="text">m</div>
        </div>
        <div class="text-wrapper">
          <div class="text">喂食数量：</div>
          <input class="foos-details2" type="number" v-model="food_number">
          <div class="text">g</div>
        </div>
        <div class="food">
          <button class="style" @click="onFoodChange">更改设置</button>
        </div>
      </div>
      <div class="data-water">
        <div class="title">
          <div class="data-title">手动换水</div>
          <div class="data-analyse">开启</div>
        </div>
        <div class="water">
          <button class="style" @click="onWaterChange" :checked="Water">开启换水</button>
        </div>
      </div>
      <div class="data-light">
        <div class="title">
          <div class="data-title">灯光设置</div>
          <div class="data-analyse" >{{style}}</div>
        </div>
        <div class="light">
          <button class="style" @click="onLight1Change" :checked="Light1">柔和模式</button>
          <button class="style" @click="onLight2Change" :checked="Light1">夜灯模式</button>
        </div>
      </div>
      <div class="data-health">
        <div class="title">
          <div class="data-title">健康监测</div>
          <div class="data-analyse">正常</div>
        </div>
      <div class="data-suggestion">鱼群健康状况良好，无生病/死亡等异常状况</div>
      <img class="image" src="/static/images/鱼.jpg" />
      </div>
    </div>
  </div>
</div>
</template>


<script>

import { connect } from "mqtt/dist/mqtt.js";
// const mqttHost = "mqtt.smartfishhome.fun"; //mqtt 服务器域名/IP
const mqttHost = "120.25.125.107"; //mqtt 服务器域名/IP
const mqttPort = 8084; //mqtt 服务器域名/IP
const mqttUrl = `wxs://${mqttHost}:${mqttPort}/mqtt`; //  mqtt连接路径

import card from '@/components/card'
export default {
  data () {
    return {
      client: {},
      Temp:0,
      Water_level:0,
      Turbidity:0,

      Water:false,
      Light1:false,
      Light2:false,
      style:'柔和模式',
      temp_l:"",
      temp_h:"",
      food_d:"",
      food_m:"",
      food_h:"",
      food_number:""
    }
  },

  components: {
  },

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

  methods: {

      onTempChange(){
        var that = this
        console.log(this.temp_l,this.temp_h)
        that.client.publish("/mysmarthome/sub",'{"target":"TEMP_L","value":'+this.temp_l+'}',function(){}) 
        setTimeout(() => {
        that.client.publish("/mysmarthome/sub",'{"target":"TEMP_H","value":'+this.temp_h+'}',function(err){
          if(!err){
             console.log("成功下发命令——更改温度设置")
          }
        })
        },500)
       },

      onFoodChange(){
        var that = this
        console.log(this.food_d,this.food_h,this.food_m,this.food_number)
        that.client.publish("/mysmarthome/sub",'{"target":"FEED_D","value":'+this.food_d+'}',function(){}) 
       setTimeout(() => {
       that.client.publish("/mysmarthome/sub",'{"target":"FEED_HO","value":'+this.food_h+'}',function(err){
        }) 
        },500)
       setTimeout(() => {
       that.client.publish("/mysmarthome/sub",'{"target":"FEED_M","value":'+this.food_m+'}',function(err){
        }) 
        },1000)
       setTimeout(() => {
       that.client.publish("/mysmarthome/sub",'{"target":"FEED_A","value":'+this.food_number+'}',function(err){
          if(!err){
             console.log("成功下发命令——更改温度设置")
          }
        }) 
        },1500)
       },

    onWaterChange(){
      var that = this
        that.client.publish("/mysmarthome/sub",'{"target":"WATER","value":1}',function(err){
          if(!err){
            console.log("成功下发命令——开启水泵")
          }
        })
    },
     onLight1Change(event){
       //console.log('柔和模式');
      var that = this
      console.log(event.mp._userTap)
      let tap = event.mp._userTap
       if(tap){
        that.client.publish("/mysmarthome/sub",'{"target":"LIGHT","value":"10"}',function(err){
          if(!err){
            console.log("成功下发命令——开启柔和模式")
          }
        })
       }
       this.style='柔和模式'
     },
     onLight2Change(event){
       //console.log('夜灯模式')
      var that = this
      console.log(event.mp._userTap)
      let tap = event.mp._userTap
      if(tap){
        that.client.publish("/mysmarthome/sub",'{"target":"LIGHT","value":"01"}',function(err){
          if(!err){
            console.log("成功下发命令——开启夜灯模式")
          }
        })
       }
       this.style='夜灯模式'
     },


},

  created () {
    // let app = getApp()
  }
}
</script>

<style lang="scss" scoped>
  .body-wrapper{
      background-color: #f7f7f7;
  .data-wrapper{
   padding: 20px; 
   display: flex;
   flex-direction: column;
    .data-food{
     background-color: #fff;
     width: 320px;
     height: 280px;
     border-radius: 20px;
     padding: 0 8px;
     box-shadow: #d6d6d6 0px 0px 5px;
     margin-top: 20px;
     .title{
       margin-top: 8px;
       display: flex;
       justify-content: space-between;
        .data-title{
       margin-top: 12px;
       color: #3d3d3d;
       font-size: 25px;
       font-weight: bold;

       }
       .data-analyse{
       margin-top: 5px;
       margin-right: 5px;
       color:#3e82ff;
       font-size: 30px;
       font-weight:bold ;
     }
     }
     .text-wrapper{
       margin-top: 18px;
       display: flex;
       justify-content: space-between; 
       .text{
         margin-left: 5px;
         margin-top: 9px;
         font-size: 19px;
         font-weight: bold;
         color: #3d3d3d;

       }
       .foos-details{
         width: 50px;
         height: 50px;
         color: #3e82ff;
         font-size: 25px;
         font-weight: bold;
         border-radius: 5%;
         border: #d6d6d6 2px solid;
         box-sizing: border-box;
       }
       .foos-details2{
         width: 150px;
         height: 50px;
         color: #3e82ff;
         font-size: 25px;
         font-weight: bold;
         border-radius: 5%;
         border: #d6d6d6 2px solid;
         box-sizing: border-box;
       }
     }
       .food{
       margin-top: 10px;
       margin-bottom: 5px;
       display: flex;
       flex-direction: column;
       .style{
        margin-top: 10px;
        background-color: #fff;
        border-radius: 20px;
        color: #3e82ff;
        padding: 6px 125px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-weight: bold;
        font-size: 16px;
        box-shadow: #d6d6d6 0px 0px 5px;
       }
     }
    }
    .data-light{
     background-color: #fff;
     width: 320px;
     height: 200px;
     border-radius: 20px;
     padding: 0 8px;
     box-shadow: #d6d6d6 0px 0px 5px;
     margin-top: 20px;
     .title{
       margin-top: 8px;
       display: flex;
       justify-content: space-between;
        .data-title{
       margin-top: 12px;
       color: #3d3d3d;
       font-size: 25px;
       font-weight: bold;

       }
       .data-analyse{
       margin-top: 5px;
       margin-right: 5px;
       color:#3e82ff;
       font-size: 30px;
       font-weight:bold ;
     }
     }
     .light{
       margin-top: 12px;
       margin-bottom: 5px;
       display: flex;
       flex-direction: column;
       .style{
        margin-top: 10px;
        background-color: #fff;
        border-radius: 20px;
        color: #3e82ff;
        padding: 6px 125px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-weight: bold;
        font-size: 16px;
        box-shadow: #d6d6d6 0px 0px 5px;
       }
     }
    }
    .data-water{
     background-color: #fff;
     width: 320px;
     height: 150px;
     border-radius: 20px;
     padding: 0 8px;
     box-shadow: #d6d6d6 0px 0px 5px;
     margin-top: 20px;
     .title{
       margin-top: 8px;
       display: flex;
       justify-content: space-between;
        .data-title{
       margin-top: 12px;
       color: #3d3d3d;
       font-size: 25px;
       font-weight: bold;

       }
       .data-analyse{
       margin-top: 5px;
       margin-right: 5px;
       color:#3e82ff;
       font-size: 30px;
       font-weight:bold ;
     }
     }
     .water{
       margin-top: 12px;
       margin-bottom: 5px;
       display: flex;
       flex-direction: column;
       .style{
        margin-top: 10px;
        background-color: #fff;
        border-radius: 20px;
        color: #3e82ff;
        padding: 6px 125px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-weight: bold;
        font-size: 16px;
        box-shadow: #d6d6d6 0px 0px 5px;
       }
     }
    }
   .data{
     background-color: #fff;
     width: 320px;
     height: 115px;
     border-radius: 20px;
     padding: 0 8px;
     box-shadow: #d6d6d6 0px 0px 5px;
     margin-top: 20px;
   
     .title{
       margin-top: 8px;
       display: flex;
       justify-content: space-between;
        .data-title{
       margin-top: 12px;
       color: #3d3d3d;
       font-size: 25px;
       font-weight: bold;

       }
       .data-analyse{
       margin-top: 5px;
       margin-right: 5px;
       color:#3e82ff;
       font-size: 30px;
       font-weight:bold ;
     }
     }
    .image{
      margin-top: 10px;
    }
    .data-suggestion{
       margin-top: 12px;
       color: #3d3d3d;
       font-size: 16px;
    }
   }
    .data-health{
     background-color: #fff;
     width: 320px;
     height: 350px;
     border-radius: 20px;
     padding: 0 8px;
     box-shadow: #d6d6d6 0px 0px 5px;
     margin-top: 20px;
   
     .title{
       margin-top: 8px;
       display: flex;
       justify-content: space-between;
        .data-title{
       margin-top: 12px;
       color: #3d3d3d;
       font-size: 25px;
       font-weight: bold;

       }
       .data-analyse{
       margin-top: 5px;
       margin-right: 5px;
       color:#3e82ff;
       font-size: 30px;
       font-weight:bold ;
     }
     }
    .image{
      margin-top: 10px;
    }
    .data-suggestion{
       margin-top: 12px;
       color: #3d3d3d;
       font-size: 16px;
    }
   }
    .data-temperature{
     background-color: #fff;
     width: 320px;
     height: 245px;
     border-radius: 20px;
     padding: 0 8px;
     box-shadow: #d6d6d6 0px 0px 5px;
     margin-top: 20px;
   
     .title{
       margin-top: 8px;
       display: flex;
       justify-content: space-between;
        .data-title{
       margin-top: 12px;
       color: #3d3d3d;
       font-size: 25px;
       font-weight: bold;

       }
       .data-analyse{
       margin-top: 5px;
       margin-right: 5px;
       color:#3e82ff;
       font-size: 30px;
       font-weight:bold ;
     }
     }
    .image{
      margin-top: 10px;
    }
    .data-suggestion{
       margin-top: 12px;
       color: #3d3d3d;
       font-size: 16px;
    }
         .text-wrapper{
       margin-top: 18px;
       display: flex;
       justify-content: space-between; 
       .text{
         margin-left: 5px;
         margin-top: 9px;
         font-size: 19px;
         font-weight: bold;
         color: #3d3d3d;

       }
       .temperature-details{
         width: 65px;
         height: 50px;
         color: #3e82ff;
         font-size: 25px;
         font-weight: bold;
         border-radius: 5%;
         border: #d6d6d6 2px solid;
         box-sizing: border-box;
       }
     }
       .temp{
       margin-top: 10px;
       margin-bottom: 5px;
       display: flex;
       flex-direction: column;
       .style{
        margin-top: 10px;
        background-color: #fff;
        border-radius: 20px;
        color: #3e82ff;
        padding: 6px 125px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-weight: bold;
        font-size: 16px;
        box-shadow: #d6d6d6 0px 0px 5px;
       }
     }
   }
  }
}
</style>

