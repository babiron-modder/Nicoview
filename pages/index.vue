<template>
  <div class="">
    <AppHeader/>
    <section class="container">
      <h1>Nicoview</h1>
      <div class="text-mute">動画が見れない場合はCookieをONにしてみてください</div>
      <p>総合 / 毎時 / カテゴリ合算 / <a href="./ranking/fav/hour/all">ランキング</a></p>
      <div v-for="item in result.rss.channel.item" class="">
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
  scrollToTop: true,
  components: {
    AppHeader,
    Card
  },
  data() {
    return {
    };
  },
  async asyncData ({ params }) {
    const { data: search_result } = await axios.get(`https://www.nicovideo.jp/ranking/genre/all?&rss=2.0&lang=ja-jp`);
    let result=xmljson.xml2js(search_result, {compact: true, spaces: 4});
    return {
      search_result,
      result
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


</style>
