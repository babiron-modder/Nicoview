<template>
  <div class="">
    <AppHeader/>
    <section class="container">
      <h1>Nicoview</h1>
      <p v-if="false">
        <span v-for="item in sort_obj_arr"><span v-if="item.key==sort_obj">{{item.jp}}</span><a :href="'/ranking/' + item.key + '/' + sort_duration + '/' + category" v-else>{{item.jp}}</a> / </span>
      </p v-if="false">
      <p>{{query_text}}</p>
      <p>
        <span v-for="item in sort_duration_arr"><span v-if="item.key==sort_duration">{{item.jp}}</span><a :href="'/ranking/' + sort_obj + '/' + item.key + '/' + category + query_text" v-else>{{item.jp}}</a> / </span>
      </p>
      <p>
        <span v-for="item in sort_category_arr"><span v-if="item.key==category">{{item.jp}}</span><a :href="'/ranking/' + sort_obj + '/' + sort_duration + '/' + item.key" v-else>{{item.jp}}</a> / </span>
      </p>
      <p v-html="taggg"></p>
      <div v-for="item in result.rss.channel.item">
        <Card :item="item"></Card>
      </div>
    </section>
  </div>
</template>

<script>
import AppHeader from '~/components/AppHeader.vue'
import Card from '~/components/Card.vue'
import axios from 'axios'
import xmljson from 'xml-js'

export default {
  components: {
    AppHeader,
    Card
  },
  data() {
    return {
      sort_obj_arr:[
        {key: "fav", jp: "総合"},
        {key: "view", jp: "再生数"},
        {key: "res", jp: "コメント数"},
        {key: "mylist", jp: "マイリスト数"},
      ],
      sort_duration_arr:[
        {key: "hour", jp: "毎時"},
        {key: "24h", jp: "24時間"},
        {key: "week", jp: "週間"},
        {key: "month", jp: "月間"},
        {key: "total", jp: "合計"},
      ],
      sort_category_arr:[
        {key: "all", jp: "カテゴリ合算"},
        {key: "hot-topic", jp: "話題"},
        {key: "entertainment", jp: "エンターテイメント"},
        {key: "radio", jp: "ラジオ"},
        {key: "music_sound", jp: "音楽・サウンド"},
        {key: "dance", jp: "ダンス"},
        {key: "animal", jp: "動物"},
        {key: "nature", jp: "自然"},
        {key: "cooking", jp: "料理"},
        {key: "traveling_outdoor", jp: "旅行・アウトドア"},
        {key: "vehicle", jp: "乗り物"},
        {key: "sports", jp: "スポーツ"},
        {key: "society_politics_news", jp: "社会・政治・時事"},
        {key: "technology_craft", jp: "技術・工作"},
        {key: "commentary_lecture", jp: "解説・講座"},
        {key: "anime", jp: "アニメ"},
        {key: "game", jp: "ゲーム"},
        {key: "other", jp: "その他"},
      ],
    };
  },
  async asyncData ({ params, query }) {
    //const { data: search_result } = await axios.get(`http://www.nicovideo.jp/ranking/${params.sort_obj}/${params.sort_duration}/${params.category}?rss=2.0`);
    //const { data: search_result } = await axios.get(`https://www.nicovideo.jp/ranking/genre/${params.category}&tag=${encodeURI(params.tag)}&term=${params.sort_duration}&rss=2.0&lang=ja-jp`);
    let hoge;
    let query_text = "";
    let hot_topic_text="genre/";
    if(params.category=="hot-topic"){
      hot_topic_text="";
    }
    if(query.tag!=undefined){
      query_text = "&tag=" + encodeURI(query.tag);
    }
    if(query.key!=undefined){
      query_text = "&key=" + encodeURI(query.key);
    }

    const { data: search_result } = await axios.get(`https://www.nicovideo.jp/ranking/${hot_topic_text}${params.category}?${query_text}&term=${params.sort_duration}&rss=2.0&lang=ja-jp`);
    let result=xmljson.xml2js(search_result, {compact: true, spaces: 4});
    //const { data: tag_search_result } = await axios.get(`https://www.nicovideo.jp/ranking/genre/${params.category}?${query_text}&term=${params.sort_duration}`);
    const { data: tag_search_result } = await axios.get(`https://www.nicovideo.jp/ranking/${hot_topic_text}${params.category}?${query_text}&term=${params.sort_duration}`);
    if(params.category!="all"){
      if(params.category=="hot-topic"){
        hoge=tag_search_result.split("HotTopicsContainer\"><ul>")[1].split("</ul></section>")[0].replace(/(\/ranking\/)/g , "").replace(/li/g, "span").replace(/<\/a>/g, "</a>/");
      }else{
        hoge=tag_search_result.split("RepresentedTagsContainer\"><ul>")[1].split("</ul></section>")[0].replace(/(\/ranking\/genre\/)/g , "").replace(/li/g, "span").replace(/<\/a>/g, "</a>/");
      }

    }
    if(query_text!=""){
      query_text = "?" + query_text;
    }

    return {
      result,
      sort_obj:params.sort_obj,
      sort_duration:params.sort_duration,
      category:params.category,
      taggg:hoge,
      query_text:query_text,
      que:query,
    }
  },
  head(){
    return {
      title: 'Nicoview (仮)'
    };
  }
}


</script>

<style>
.RankingFilterTag_active, .RankingFilterTag_active{
  pointer-events: none;
  color: black;
}

</style>
