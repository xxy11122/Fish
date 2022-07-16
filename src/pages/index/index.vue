<template>
<div>
 <div class="back1">
   <div class="title1">我的鱼缸</div>
   <div class="add"> 
    <div class="aimg">
      <img class="img" src="/static/images/增加.png" />
    </div>
    <div class="abuttom">
      <button class="button">添加鱼缸</button>
    </div>


  </div>
   </div>

 <div class="back2">
   <div class="title2">全部鱼缸</div>
   <div class="one">
     <div class="onetop">
       <div class="onew">鱼缸一号</div>
       <div>
         <button class="button1" @click=setting()>设置</button>
       </div>


     </div>
     
     <div>
     <img class="img1" src="/static/images/鱼.jpg" />
  
     </div>
     

   </div>

  </div>


</div>


 
</template>

<script>

import card from '@/components/card'
export default {
  data () {
    return {
    }
  },

  components: {
  },

  methods: {
     setting(){
       wx.navigateTo({
       url: '/pages/setting/main'})
  }
  },


  created () {
    // let app = getApp()
  }
}
</script>


<style lang="scss" scoped>
.back1{
     display: flex;
     flex-direction: column;
     width: 400px;
     height: 200px;
      background-color: #3e82ff;}
.title1{
     margin-left: 10px;
     margin-top: 20px;
     color: #fff;
     font-size: 24px;
     font-weight: bold;
     
   }

.back2{
      width: 400px;
     height: 180px;
      background-color: #ffffff;}
.title2{
     margin-left: 10px;
     color: #3e82ff;
     font-size: 20px;
     font-weight: bold;
     margin-bottom: 5px;
     margin-top: 20px;
     
     
   }

.add{
    display: flex;
    justify-content: space-between;
    background-color: #fff;
    margin-top: 25px;
    margin-left: 20px;
    width: 320px;
    height: 100px;
    border-radius: 20px;
    padding: 0 8px;
    box-shadow: #d6d6d6 0px 0px 5px;
}
.img{
  margin-top: 5px;
  width: 90px;
  height: 90px;
  margin-left: 20px;

}
.button{
  width:100px;
  margin-top: 25px;
  background-color: #3e82ff;
  border-radius: 20px;
    color: #fff;
    padding: 6px 6px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-weight: bold;
    font-size: 16px;
    box-shadow: #d6d6d6 0px 0px 5px;
    margin-right: 20px;
   

}

.one{
  background-color: #fff;
  width: 160px;
  height: 145px;
  margin-left: 20px;
  margin-top: 20px;
  border-radius: 20px;
  box-shadow: #d6d6d6 0px 0px 5px;
  .onetop{
    display: flex;
    justify-content: space-between;
    margin-bottom: 5px;

  }
  .onew{

    margin-top: 7px;margin-left: 20px;
  }

  .img1{
  width: 140px;
  height: 100px;
  margin-left: 10px;

}
  .button1{
    margin-top: 5px;
    background-color: #3e82ff;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-content: center;
    color: #fff;
    padding: 0 0 0 0;
    width:35px;
    height: 25px;
    font-weight: normal;
    font-size: 12px;
    box-shadow: #d6d6d6 0px 0px 5px;
    margin-right: 20px;
}
}
</style>

