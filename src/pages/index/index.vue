<template>
 <div class="body-wrapper">
   <div class="body">
     <div class="data-wrapper">
       <div class="data">
         <img class="data-logo" src="/static/images/健康监测.png" />
         <div class="data-text">
           <div class="data-title">健康监测</div>
           <div class="data-description">打开</div>
         </div>
       </div>
      <div class="data">
         <img class="data-logo" src="/static/images/温度.png" />
         <div class="data-text">
           <div class="data-title">温度</div>
           <div class="data-description">打开</div>
         </div>
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
  
  },

  created () {
    // let app = getApp()
  }
}
</script>

<style lang="scss" scoped>
.data-wrappe{
 margin-top: 20 px;
 display: flex;
 .data{
   background-color: #fff;
   width: 300 px;
   height: 80 px;
   border-radius: 20px;
   display: flex;
   justify-content: space-around;
   padding: 0 8px;
   box-shadow: #d6d6d6 0px 0px 5px;
 }
}
</style>

