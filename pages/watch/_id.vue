<template>
  <div class="">
    <div class="">
      <iframe :src="'https://embed.nicovideo.jp/watch/'+this.url" width="" height="" class="" style="height:100%;width:100%;position:absolute;border:none;" v-if="true" ref="mov_area"></iframe>
      <button class="btn p-0 fullscreen" @click="fullscreen">
        <i class="material-icons m-1">open_in_full</i>
      </button>
    </div>
    <div class="container" style="padding-top:105vh;min-height:200vh">
      <h3 v-html='result.niconico_response.video.title'></h3>
      <p v-html='result.niconico_response.video.first_retrieve.split("+")[0].replace(/-/g,"/").replace(/T/g," ")+" 投稿"'></p>
      <p v-html='"再生数: "+result.niconico_response.video.view_counter+" コメント数: "+result.niconico_response.thread.num_res+" マイリスト数: "+result.niconico_response.video.mylist_counter'></p>
      <p style="overflow: auto;">元動画URL: <a style="overflow-wrap: normal;" :href='"https://www.nicovideo.jp/watch/"+this.url'>{{"https://www.nicovideo.jp/watch/"+this.url}}</a></p>
      <p v-if="len"><a v-for="tag in result.niconico_response.tags.tag_info" class="btn btn-info m-1" :href="'/search/'+encodeURIComponent(tag.tag)+'?targets=tagsExact&_offset=0'">{{tag.tag}}</a></p>
      <div style="overflow:auto;" v-html='(result.niconico_response.video.description+"").replace(/(watch\/\d+)/g, `<a href="/$1">$1</a>`).replace(/(s[mo]\d+)/g, `<a href="/watch/$1">$1</a>`).replace(/\n/g,"<br>").replace(/(((http:\/\/)|(https:\/\/))[\w\.\/%\+#=\?\-@\(\)\$&]+)+/g, "<a href=\"$1\">$1</a>").replace(/(mylist\/\d+)/g, `<a href="https://www.nicovideo.jp/$1">$1</a>`)'></div>
      <!-- <p>{{result}}</p> -->
      <!-- <p>{{URL_search_result}}</p> -->
      <div class="pt-5 pb-4" >
        <h4>『{{search_word}}』の検索結果</h4>
        <!-- 検索結果 表示欄 -->
        <div class="border border-dark rounded" style="height:70vh;overflow:auto;overflow-y: scroll;" ref="card_list_area" id="card_list_area">
          <!-- カード -->
          <div class="border m-2" v-for="item in URL_search_result.data" :style="(item.contentId==url)?'background-color:#dcdcdc':''" :ref="(item.contentId==url)?'current_url_card':''">
            <div class="d-flex">
              <!-- 画像 -->
              <div class="d-flex flex-column align-items-end" style="background:black">
                <div class="flex-fill d-flex align-content-center">
                  <div class="align-self-center">
                    <a :href="'/watch/'+item.contentId"><img v-bind:src="item.thumbnailUrl" alt=""></a>
                  </div>
                </div>
                <div class="">
                  <p class="text-light px-1" style="background:rgba(0,0,0,0.75);margin:-24px 0 0 0;height:24px">{{Math.floor((item.lengthSeconds*1)/60)}}:{{(item.lengthSeconds*1%60<10)?"0":""}}{{item.lengthSeconds*1%60}}</p>
                </div>
              </div>
              <!-- タイトル -->
              <div  class="px-3 py-1 my-auto h-100 align-middle flex-fill" style="width:0px;word-wrap:break-word;">
                <p class="my-2"><a :href="'/watch/'+item.contentId" v-html="item.title"></a></p>
              </div>
            </div>
            <!-- 説明欄 -->
            <div class="mx-2">
              <p class="m-0 w-100 text-truncate">
                <span class="mr-1">再生数: {{item.viewCounter}}</span>
                <span class="mx-1">コメント数: {{item.commentCounter}}</span>
                <span class="mx-1">マイリスト数: {{item.mylistCounter}}</span>
              </p>
              <p class="m-0">{{item.startTime.split("+")[0].replace(/-/g,"/").replace(/T/g," ")+" 投稿"}}</p>
            </div>
            <!-- <p>{{item}}</p> -->
          </div>
        </div>
        <p class="text-right mt-0">※検索結果は『<a href="https://site.nicovideo.jp/search-api-docs/snapshot">ニコニコ動画 スナップショット検索API v2</a>』の仕様により、毎日AM5:00に更新されます。</p>
      </div>
    </div>
  </div>
</template>

<script>
import AppHeader from '~/components/AppHeader.vue'
import axios from 'axios'
import xmljson from 'xml-js'

export default {
  components: {
    AppHeader
  },
  data() {
    return {

    };
  },
  validate({params}) {
    return /^(s[mo]|\d\d)\d+$/.test(params.id);
  },
  methods: {
    fullscreen(){
      let requestFullScreen = this.$refs.mov_area.requestFullscreen || this.$refs.mov_area.mozRequestFullScreen || this.$refs.mov_area.webkitRequestFullScreen || this.$refs.mov_area.msRequestFullscreen;
      requestFullScreen.call(this.$refs.mov_area);
      // console.log(this.$refs.mov_area);
    }
  },
  async asyncData ({ params }) {
    let id = encodeURI(params.id);

    const { data: search_result } = await axios.get(`http://api.ce.nicovideo.jp/nicoapi/v1/video.info?v=${id}`);
    // let result = xmljson.xml2js(search_result, {compact: true, spaces: 4});
    let result = search_result;
    console.log(result);
    let len = result.niconico_response.tags.tag_info.length>2;

    let search_word = result.niconico_response.video.title;
    search_word = search_word
      .replace(/\s+/g,' ')
      .replace(/[⓪①②③④⑤⑥⑦⑧⑨⑩⑪⑫⑬⑭⑮⑯⑰⑱⑲⑳㉑㉒㉓㉔㉕㉖㉗㉘㉙㉚㉛㉜㉝㉞㉟㊱㊲㊳㊴㊵㊶㊷㊸㊹㊺㊻㊼㊽㊾㊿]/g, " ")
      .replace(/[⓿❶❷❸❹❺❻❼❽❾❿⓫⓬⓭⓮⓯⓰⓱⓲⓳⓴]/g, " ")
      .replace(/\s*[#＃♯]\s*[\d１２３４５６７８９０]+\s*.*「*.*」*/g," ")
      .replace(/\s*part\.*\s*[\d１２３４５６７８９０]+\s*\/*[\d１２３４５６７８９０]*.*/gi," ")
      .replace(/\s*パート\.*\s*[\d１２３４５６７８９０]+\s*\/*[\d１２３４５６７８９０]*.*/gi," ")
      .replace(/\s*\(\s*[\d１２３４５６７８９０]+\s*\/*[\d１２３４５６７８９０]*\)/gi," ")
      .replace(/\s*【\s*[\d１２３４５６７８９０一二三四五六七八九十零]+\s*】/gi," ")
      .replace(/\s*第[\d１２３４５６７８９０一二三四五六七八九十零]+話.*/gi," 話 ")
      .replace(/\s*[\d１２３４５６７８９０一二三四五六七八九十零]+話.*/gi," 話 ")
      .replace(/\s*第[\d１２３４５６７８９０一二三四五六七八九十零]+回/gi," 回 ")
      .replace(/\s*第[\d１２３４５６７８９０一二三四五六七八九十零]+戦/gi," 戦 ")
      .replace(/\s*[\d１２３４５６７８９０一二三四五六七八九十零]+日目/gi," 日目 ")
      .replace(/\s*その[\d１２３４５６７８９０一二三四五六七八九十零]+/gi," その ")
      .replace(/\s*[前中後]編\s*/gi," ")
      .replace(/\s*最終[話回]\s*/gi," ")
      .replace(/\s+\-*\s*/gi," ")
      .replace(/\s*chapter[\d１２３４５６７８９０]+\s*/gi," chapter ")
      .replace(/\s+[\d１２３４５６７８９０]+\s+/gi," ")
      .replace(/\s*#FINAL\s*/g," ")

    const { data: URL_search_result } = await axios.get("https://api.search.nicovideo.jp/api/v2/snapshot/video/contents/search?q="+encodeURIComponent(search_word)+"&targets=title&_sort=-startTime&_context=hoge&&fields=contentId,title,description,viewCounter,commentCounter,mylistCounter,startTime,thumbnailUrl,lengthSeconds&_limit=100");

    return {
      search_result,
      URL_search_result,
      search_word,
      result,
      len,
      url:params.id
    }
  },
  mounted: function () {
    this.$nextTick(() => {
      const options = {
        root: null,
        rootMargin: "0px",
        threshold: 1.0
      };
      const callback = (entries, observer) => {
        entries.forEach(entry => {
          if (entry.isIntersecting){
            this.$refs.current_url_card[0].scrollIntoView();
          }
        })
      }
      const observer = new IntersectionObserver(callback, options);
      observer.observe(this.$refs.card_list_area)
    });
  },
  head(){
    return {
      title: `${this.result.niconico_response.video.title}`,
      script: [
        { src: 'https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js' }
      ],
      link:[
        {rel: 'stylesheet', href: 'https://fonts.googleapis.com/icon?family=Material+Icons'}
      ]
    };
  }
}


</script>

<style>

#card_list_area{
  scroll-behavior: smooth;
}
.fullscreen{
  background-color: #f0f0f0aa;
  height: 40px;
  width: 40px;
  z-index: 5;
  position: absolute;
  right: 0px;
  bottom: -40px;
}

</style>
