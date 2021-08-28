<template>
  <div class="border border-dark rounded fill mb-3">
    <!-- 画像とタイトル -->
    <div class="d-flex">
      <!-- 画像 -->
      <div class="d-flex flex-column align-items-end" style="background:black">
        <div class="flex-fill d-flex align-content-center">
          <div class="align-self-center" style="">
            <a :href="item.link._text.split('.jp')[1]">
              <img :src='item.description._cdata.split(` src="`)[1].split(`" width=`)[0]' alt="" class="">
            </a>
          </div>
        </div>
        <div class="">
          <p class="text-light px-1" style="background:rgba(0,0,0,0.75);margin:-24px 0 0 0;height:24px">{{item.description._cdata.split('\"nico-info-length\"&gt;')[1].split('&lt;/strong')[0]}}</p>
        </div>
      </div>
      <!-- タイトル -->
      <div class="px-3 py-1 my-auto h-100 align-middle flex-fill" style="width:0px;word-wrap:break-word;">
        <p style="margin:0px"><a v-html="item.title._text" :href="item.link._text.split('.jp')[1].split('?')[0]"></a> &#091;<a :href="item.link._text.replace('www','embed')">embed</a>&#093; &#091;<a :href="item.link._text">nicnico</a>&#093;</p>
        <!--p style="word-wrap:break-word;">{{item.description._cdata}}</p-->
      </div>
    </div>
    <!-- 説明ボタン -->
    <button type="button" name="button" class="btn btn-secondary rounded-0 w-100 text-truncate" @click="toggleCollapse">
      <!--span class="mx-1">合計: {{item.description._cdata.split('\"nico-info-number\"&gt;')[1].split('&lt;/strong')[0]}}pts</span-->
      <span class="mx-1">再生数: {{item.description._cdata.split('\"nico-info-total-view\"&gt;')[1].split('&lt;/strong')[0]}}</span>
      <span class="mx-1">コメント数: {{item.description._cdata.split('\"nico-info-total-res\"&gt;')[1].split('&lt;/strong')[0]}}</span>
    </button>
    <!-- 説明欄 -->
    <transition name="fade">
      <div class="m-3 overflow-hidden" v-if="isCollapsed">
        <div class="border mb-1">
          <div v-html='this.description.replace(/(watch\/\d+)/g, `<a href="/$1">$1</a>`).replace(/(s[mo]\d+)/g, `<a href="/watch/$1">$1</a>`)' :style="'word-wrap:break-word; max-height:'+open_height" class="overflow-hidden"></div>
          <button type="button" name="button" class="btn btn-sm btn-outline-dark w-100" @click="toggleOpen" v-if="!isDescriptionGet">{{open_str}}</button>
        </div>
        <div v-html='`<p class="nico-info">`+item.description._cdata.split(`border=\"0\"/></p>`)[1].split(`<p class="nico-info">`)[1]' style="word-wrap:break-word;"></div>
        <!-- <p style="word-wrap:break-word;" v-if="false">{{item.description._cdata}}</p> -->
      </div>
    </transition>
  </div>
</template>

<script>
import axios from 'axios'
import xmljson from 'xml-js'

export default {
  components: {

  },
  data() {
    return {
      isCollapsed:false,
      isOpen:false,
      open_height:"50px",
      open_str:"open",
      description: this.item.description._cdata.split(`border=\"0\"/></p>`)[1].split(`<p class="nico-info">`)[0],
      isDescriptionGet: false
    };
  },
  props: {
    item: Object
  },
  methods:{
    toggleCollapse:function(){
      this.isCollapsed=!this.isCollapsed;
    },
    // 説明欄の追加情報
    toggleOpen:function(){
      this.isOpen=!this.isOpen;
      this.open_str=(this.isOpen)?"close":"open";
      this.open_height=(this.isOpen)?"1000px":"50px";

      if(this.isDescriptionGet==true)return;
      this.isDescriptionGet=true;

      const search_result = axios.get('/api/nicoapi/v1/video.info?v=' + this.item.guid._text.split('watch/')[1])
      .then( search_result => {
        // this.description = xmljson.xml2js(search_result.data, {compact: true, spaces: 4});
        // let description = xmljson.xml2js(search_result.data, {compact: true, spaces: 4}).nicovideo_video_response.video.description._text;
        let description = search_result.data.niconico_response.video.description;

        if(description==undefined){
          this.description='';
        }else {
          this.description=description;
        }

      })

    },
  },
  head(){
    return {
    };
  },
}
</script>

<style scoped>
.fade-enter-active {
  transition: all .3s ease-out;
}
.fade-leave-active {
  transition: all .2s ease-out;
}
.fade-enter, .fade-leave-to{
  transform: translateY(-20px);
  max-height: 0px;
}

.fade-enter-to, .fade-leave{
  max-height: 1000px;
}

.nico-info{
  margin:0;
}

</style>
