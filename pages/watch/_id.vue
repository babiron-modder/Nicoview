<template>
  <div class="">
    <div class="">
      <iframe :src="'https://embed.nicovideo.jp/watch/'+this.url" width="" height="" class="" style="height:100%;width:100%;position:absolute;border:none;" v-if="true"></iframe>
    </div>
    <div class="container" style="padding-top:105vh;min-height:200vh">
      <h3 v-html='result.nicovideo_video_response.video.title._text'></h3>
      <p v-html='result.nicovideo_video_response.video.first_retrieve._text.split("+")[0].replace(/-/g,"/").replace(/T/g," ")+" 投稿"'></p>
      <p v-html='"再生数: "+result.nicovideo_video_response.video.view_counter._text+" コメント数: "+result.nicovideo_video_response.thread.num_res._text+" マイリスト数: "+result.nicovideo_video_response.video.mylist_counter._text'></p>
      <p>元動画URL: <a :href='"https://www.nicovideo.jp/watch/"+this.url'>{{"https://www.nicovideo.jp/watch/"+this.url}}</a></p>
      <p v-if="len"><a v-for="tag in result.nicovideo_video_response.tags.tag_info" class="btn btn-info m-1" :href="'/search/'+encodeURI(tag.tag._text)+'&targets=tagsExact'">{{tag.tag._text}}</a></p>
      <p v-html='(result.nicovideo_video_response.video.description._text+"").replace(/(watch\/\d+)/g, `<a href="/$1">$1</a>`).replace(/(s[mo]\d+)/g, `<a href="/watch/$1">$1</a>`).replace(/\n/g,"<br>").replace(/(((http:\/\/)|(https:\/\/))[\w\.\/%\+#=\?\-@\(\)\$&]+)+/g, "<a href=\"$1\">$1</a>").replace(/(mylist\/\d+)/g, `<a href="https://www.nicovideo.jp/$1">$1</a>`)' style=""></p>
      <!-- <p>{{result}}</p> -->
      <!-- <p>{{URL_search_result}}</p> -->
      <div class="pt-5 pb-5" >
        <h4>『{{search_word}}』の検索結果</h4>
        <!-- 検索結果 表示欄 -->
        <div class="border border-dark rounded mb-3" style="height:70vh;overflow:auto;overflow-y: scroll;" ref="card_list_area" id="card_list_area">
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
  async asyncData ({ params }) {
    let id = encodeURI(params.id);

    const { data: search_result } = await axios.get(`http://api.ce.nicovideo.jp/nicoapi/v1/video.info?v=${id}`);
    let result = xmljson.xml2js(search_result, {compact: true, spaces: 4});
    let len = result.nicovideo_video_response.tags.tag_info.length>2;

    let search_word = result.nicovideo_video_response.video.title._text;
    search_word = search_word
      .replace(/\s+/g,' ')
      .replace(/[⓪①②③④⑤⑥⑦⑧⑨⑩⑪⑫⑬⑭⑮⑯⑰⑱⑲⑳㉑㉒㉓㉔㉕㉖㉗㉘㉙㉚㉛㉜㉝㉞㉟㊱㊲㊳㊴㊵㊶㊷㊸㊹㊺㊻㊼㊽㊾㊿]/g, " ")
      .replace(/[⓿❶❷❸❹❺❻❼❽❾❿⓫⓬⓭⓮⓯⓰⓱⓲⓳⓴]/g, " ")
      .replace(/\s*[#＃♯]\s*[\d１２３４５６７８９０]+\s*.*「*.*」*/g," ")
      .replace(/\s*part\.*\s*[\d１２３４５６７８９０]+\s*\/*[\d１２３４５６７８９０]*.*/gi," ")
      .replace(/\s*パート\.*\s*[\d１２３４５６７８９０]+\s*\/*[\d１２３４５６７８９０]*.*/gi," ")
      .replace(/\s*\(\s*[\d１２３４５６７８９０]+\s*\/*[\d１２３４５６７８９０]*\)/gi," ")
      .replace(/\s*【\s*[\d１２３４５６７８９０一二三四五六七八九十零]+\s*】/gi," ")
      .replace(/\s*[\d１２３４５６７８９０一二三四五六七八九十零]+話.*/gi,"  話 ")
      .replace(/\s*第[\d１２３４５６７８９０一二三四五六七八九十零]+回/gi," 第 回 ")
      .replace(/\s*第[\d１２３４５６７８９０一二三四五六七八九十零]+戦/gi," 第 戦 ")
      .replace(/\s*[\d１２３４５６７８９０一二三四五六七八九十零]+日目/gi," 日目 ")
      .replace(/\s*その[\d１２３４５６７８９０一二三四五六七八九十零]+/gi," その ")
      .replace(/\s*[前中後]編\s*/gi," ")
      .replace(/\s*最終話\s*/gi," ")
      .replace(/\s*chapter[\d１２３４５６７８９０]+\s*/gi," chapter ")
      .replace(/\s+[\d１２３４５６７８９０]+\s+/gi," ")

    const { data: URL_search_result } = await axios.get("https://api.search.nicovideo.jp/api/v2/snapshot/video/contents/search?q="+encodeURI(search_word)+"&targets=title&_sort=-startTime&_context=hoge&&fields=contentId,title,description,viewCounter,commentCounter,mylistCounter,startTime,thumbnailUrl,lengthSeconds&_limit=100");


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
      // this.$refs.card_list_area.scrollIntoView();
      // this.$refs.current_url_card[0].scrollIntoView();
      // this.$refs.current_url_card[0].scrollTop = 100;
      // this.$refs.current_url_card[0].inview(function(){
      //   this.$refs.current_url_card[0].scrollIntoView();
      // });

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
      title: `${this.result.nicovideo_video_response.video.title._text}`
    };
  }
}


</script>

<style>

#card_list_area{
  scroll-behavior: smooth;
}


</style>
