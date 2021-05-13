<template>
  <div class="">
    <AppHeader />
    <section class="container">
      <h1>Nicoview</h1>
      <p>{{(target=="")?"tag検索":"検索"}}:「{{search_word}}」  page: {{offset*1}}~{{offset*1+100}}</p>
      <p class='mb-4'>
        <a :class='(offset*1!=0)?"btn btn-primary btn-sm":"btn btn-primary btn-sm disabled"' :href="'/search/'+url+'?targets='+$route.query.targets+'&_offset='+((offset*1<100)?0:(offset*1-100))">&lt;&lt;</a>
        <a class='btn btn-primary btn-sm ml-3' :href="'/search/'+url+'?targets='+$route.query.targets+'&_offset='+((offset*1>1500)?1600:(offset*1+100))" v-if="offset*1!=1600">&gt;&gt;</a>
      </p>
      <ul v-for="item in search_result.data" class="p-0" style="list-style-type: none">
        <li class="">
          <div class="d-flex">

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
            <div  class="px-3 py-1 my-auto h-100 align-middle flex-fill" style="width:0px;word-wrap:break-word;">
              <p class="my-2"><a :href="'/watch/'+item.contentId" v-html="item.title"></a></p>
            </div>

          </div>
          <p class="m-0">
            <span class="mr-1">再生数: {{item.viewCounter}}</span>
            <span class="mx-1">コメント数: {{item.commentCounter}}</span>
            <span class="mx-1">マイリスト数: {{item.mylistCounter}}</span>
          </p>
          <p class="m-0">{{item.startTime.split("+")[0].replace(/-/g,"/").replace(/T/g," ")+" 投稿"}}</p>
        </li>
      </ul>
      <!-- <ul v-for="item in search_result.data" v-show="false">
        <li>{{item}}</li>
      </ul> -->
      <p class='mb-4'>
        <a :class='(offset*1!=0)?"btn btn-primary btn-sm":"btn btn-primary btn-sm disabled"' :href="'/search/'+url+'?targets='+$route.query.targets+'&_offset='+((offset*1<100)?0:(offset*1-100))">&lt;&lt;</a>
        <a class='btn btn-primary btn-sm ml-3' :href="'/search/'+url+'?targets='+$route.query.targets+'&_offset='+((offset*1>1500)?1600:(offset*1+100))" v-if="offset*1!=1600">&gt;&gt;</a>
      </p>
    </section>
  </div>
</template>

<script>
import AppHeader from '~/components/AppHeader.vue'
import axios from 'axios'

export default {
  components: {
    AppHeader
  },
  data() {
    return {
      hoge: "hello search! "+"huga"
    };
  },
  validate({params}) {
    return true;
  },
  async asyncData ({ params, query }) {
    let target="&targets="+query.targets;

    let word = encodeURIComponent(params.word.split('&')[0]);
    let word_before_encode = params.word.split('&')[0]

    let offset = query._offset;
    console.log(offset);

    //const { data: search_result } = await axios.get(`https://api.search.nicovideo.jp/api/v2/video/contents/search?q=${word}${target}&_sort=-startTime&_context=hoge&&fields=contentId,title,description,viewCounter,commentCounter,mylistCounter,startTime,thumbnailUrl,lengthSeconds&_limit=100`);
    const { data: search_result } = await axios.get(`https://api.search.nicovideo.jp/api/v2/snapshot/video/contents/search?q=${word}&_offset=${offset*1}${target}&_sort=-startTime&_context=hoge&&fields=contentId,title,description,viewCounter,commentCounter,mylistCounter,startTime,thumbnailUrl,lengthSeconds&_limit=100`);


    return {
      search_word: word_before_encode,
      search_result,
      url:word,
      offset: offset,
      target:target
    }
  },
  head(){
    return {
      title: `${decodeURIComponent(this.url.split('&')[0])}`
    };
  }
}


</script>

<style>

</style>
