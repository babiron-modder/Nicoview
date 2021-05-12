<template>
  <div class="">
    <div class="">
      <iframe :src="'https://embed.nicovideo.jp/watch/'+this.url" width="" height="" class="" style="height:100%;width:100%;position:absolute;border:none;" v-if="true" ref="mov_area"></iframe>
      <button class="btn p-0 fullscreen" @click="fullscreen">
        <i class="material-icons m-1">open_in_full</i>
      </button>
    </div>
    <div class="container" style="padding-top:105vh;min-height:200vh">
      <h3 v-html='result.nicovideo_video_response.video.title._text'></h3>
      <p v-html='result.nicovideo_video_response.video.first_retrieve._text.split("+")[0].replace(/-/g,"/").replace(/T/g," ")+" 投稿"'></p>
      <p v-html='"再生数: "+result.nicovideo_video_response.video.view_counter._text+" コメント数: "+result.nicovideo_video_response.thread.num_res._text+" マイリスト数: "+result.nicovideo_video_response.video.mylist_counter._text'></p>
      <p style="overflow: auto;">元動画URL: <a style="overflow-wrap: normal;" :href='"https://www.nicovideo.jp/watch/"+this.url'>{{"https://www.nicovideo.jp/watch/"+this.url}}</a></p>
      <p v-if="len"><a v-for="tag in result.nicovideo_video_response.tags.tag_info" class="btn btn-info m-1" :href="'/search/'+encodeURI(tag.tag._text)+'&targets=tagsExact'">{{tag.tag._text}}</a></p>
      <div style="overflow:auto;" v-html='(result.nicovideo_video_response.video.description._text+"").replace(/(watch\/\d+)/g, `<a href="/$1">$1</a>`).replace(/(s[mo]\d+)/g, `<a href="/watch/$1">$1</a>`).replace(/\n/g,"<br>").replace(/(((http:\/\/)|(https:\/\/))[\w\.\/%\+#=\?\-@\(\)\$&]+)+/g, "<a href=\"$1\">$1</a>").replace(/(mylist\/\d+)/g, `<a href="https://www.nicovideo.jp/$1">$1</a>`)'></div>
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
    let result = xmljson.xml2js(search_result, {compact: true, spaces: 4});
    let len = result.nicovideo_video_response.tags.tag_info.length>2;

    console.log(result.nicovideo_video_response.tags.tag_info);
// result = {
//   _declaration: { _attributes: { version: '1.0', encoding: 'UTF-8' } },
//   nicovideo_video_response: {
//     _attributes: { status: 'ok' },
//     video: {
//       id: { _text: 'sm36542104' },
//       user_id: { _text: '50602552' },
//       deleted: { _text: '0' },
//       title: { _text: '【クトゥルフ神話TRPG】DAY3 #FINAL' },
//       description: {
//         _text: '「叶いそうにない、いい夢だ」\n' +
//           '\n' +
//           '\n' +
//           'モザビ縛りでドン勝出来たら次に着手しよう\n' +
//           '\n' +
//           '\n' +
//           'DAY3 part0：sm35273108\n' +
//           'sm36358738 ← 前 / 次 → 3,000,000,000,000,000年後\n' +
//           '\n' +
//           'CoC動画まとめ→mylist/62438137\n' +
//           'twitter：https://twitter.com/yodosumiori\n' +
//           'pixiv：https://www.pixiv.net/member.php?id=15070076'
//       },
//       length_in_seconds: { _text: '723' },
//       thumbnail_url: {
//         _text: 'http://nicovideo.cdn.nimg.jp/thumbnails/36542104/36542104.73351713'
//       },
//       upload_time: { _text: '2020-03-21T01:10:39+09:00' },
//       first_retrieve: { _text: '2020-03-20T16:22:02+09:00' },
//       default_thread: { _text: '1584688922' },
//       view_counter: { _text: '86954' },
//       mylist_counter: { _text: '691' },
//       genre: { key: [Object], label: [Object] },
//       option_flag_community: { _text: '0' },
//       option_flag_nicowari: { _text: '0' },
//       option_flag_middle_thumbnail: { _text: '1' },
//       option_flag_dmc_play: { _text: '1' },
//       community_id: {},
//       vita_playable: { _text: 'OK' },
//       ppv_video: { _text: '0' },
//       permission: {},
//       provider_type: { _text: 'regular' },
//       options: { _attributes: [Object] }
//     },
//     thread: {
//       id: { _text: '1584688922' },
//       num_res: { _text: '439' },
//       summary: {
//         _text: 'エロかっこいい 初期値が成功するのは当たり前だろ?(鍵開けトカゲ感) これはァ?!! リアルEDUよwww はよドン勝せい これ、後でクリティカル出されたら敵わんな 続きまだー? ここでエタるのかぁ 十文字ちゃんほんとめちゃくちゃにしたい 2021年...'
//       },
//       community_id: {},
//       group_type: { _text: 'default' }
//     },
//     tags: {
//   	  tag_info: [
//   	    { tag: { _text: 'クトゥルフ神話TRPG' }, area: { _text: 'jp' } },
//   	    { tag: { _text: '高速卓リンク' }, area: { _text: 'jp' } },
//   	    { tag: { _text: 'trpg' }, area: { _text: 'jp' } },
//   	    { tag: { _text: 'ゆっくりTRPG' }, area: { _text: 'jp' } },
//   	    { tag: { _text: '品口凸凹（KP）' }, area: { _text: 'jp' } },
//   	    { tag: { _text: 'オリジナル卓リンク' }, area: { _text: 'jp' } },
//   	    { tag: { _text: 'TRPGリプレイ' }, area: { _text: 'jp' } },
//   	    { tag: { _text: 'DAY3' }, area: { _text: 'jp' } },
//   	    { tag: { _text: '次→sm38374392' }, area: { _text: 'jp' } },
//   	    { tag: { _text: 'フラグ乱立' }, area: { _text: 'jp' } }
//   	  ]
//   	}
//   }
// }


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
      title: `${this.result.nicovideo_video_response.video.title._text}`,
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
