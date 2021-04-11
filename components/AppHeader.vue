<template>
  <header>
    <div class="navbar navbar-dark bg-dark sticky-top">
      <button class="navbar-toggler text-light p-0 border-0" type="button" @click="toggleMenuOpen">
        <i class="material-icons m-1">info</i>
      </button>
      <transition name="toggle-menu">
        <div class="menu-area p-3" v-show="menu_opened">
          <div class="bg-light mb-3 p-3">
            <p class="font-weight-bold">このサイトについて</p>
            <p>このサイトは、ニコニコ動画のコンテンツを個人的に楽しむために作成されたサイトです。</p>
            <p>バグや本サイトの仕様によって見づらい点があると思いますが、個人的に楽しんでいるものなので、修正されない可能性が高いです。ご了承ください。</p>
            <p>また、このサイトを見つけてくださったコアなファンの方には申し訳ないのですが、権利の関係や個人的な事情で事前の通達なしに公開停止になる場合があります。</p>
            <p>公開停止になるその時まで、このサイトを楽しんでいただければ幸いです。</p>
          </div>
          <div class="bg-light mb-3 p-3">
            <p class="font-weight-bold">更新履歴</p>
            <p>2021/04/11　とりあえず完成</p>
            <hr class="mb-1">
            <span><a href="https://github.com/babiron-modder/nicoview">https://github.com/babiron-modder/nicoview</a></span>
          </div>
        </div>
      </transition>

      <a class="navbar-brand text-light float-left" href="/">Nicoview (仮)</a>

      <!-- Button trigger modal -->
      <button type="button" class="btn text-light p-0" @click="toggleOpen">
        <i class="material-icons m-1">search</i>
      </button>

      <transition name="toggle-serach">
        <!-- 検索ボックス -->
        <div class="input-group mt-3 mb-1" v-show="opened">
          <select class="px-2" name="" id="search_mode">
            <option value="">検索</option>
            <option value="&targets=tagsExact">tag検索</option>
          </select>

          <input type="text" class="form-control" placeholder="serach:" aria-label="sss" aria-describedby="basic-addon2" id="serach_box" @keydown.enter="entered">
          <div class="input-group-append">
            <button class="btn btn-info" type="button" @click="entered">検索</button>
          </div>
        </div>
      </transition>

    </div>
    <button class="Page-Btn" type="button" @click="scrollTop" title="ページ先頭へ戻る">
      <i class="material-icons up_icon">arrow_upward</i>
    </button>
  </header>
</template>

<script>
export default {
  head(){
    return {
      script: [
        { src: 'https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js' }
      ],
      link:[
        {rel: 'stylesheet', href: 'https://fonts.googleapis.com/icon?family=Material+Icons'}
      ]
    }
  },
  data(){
    return {
      opened:false,
      menu_opened:false
    }
  },
  methods:{
    toggleOpen: function(){
      this.opened=!this.opened;
    },
    toggleMenuOpen: function(){
      this.menu_opened=!this.menu_opened;
    },
    focusSerachBtn: function(){
      if(this.opened){
        document.getElementById("serach_box").focus();
      }
    },
    scrollTop: function(){
      window.scrollTo({
        top: 0,
        behavior: "smooth"
      });
    },
    // 検索実行
    entered: function(){
      let search_word=document.getElementById("serach_box").value;
      if(search_word.match(/^\s*$/g))return;
      let search_mode=document.getElementById("search_mode").value;
      window.location.href="/search/"+encodeURI(document.getElementById("serach_box").value)+"&_offset=0"+search_mode;
    }
  }
}
</script>

<style>
  .apptitle{
    padding-left: 50px;
  }
  .Page-Btn{
    position: fixed;
    right: 14px;
    bottom: 14px;
    width: 48px;
    height: 48px;
    text-align: center;
    border-radius: 50%;
    border-width: 0px;
    background: #5bc8ac;
    color: #ffffff;
  }
  .up_icon{
    font-size: 32px;
    margin-top: 4px;
    margin-left: 1px;
  }
  .toggle-serach-enter-active {
    transition: all .3s ease;
  }
  .toggle-serach-enter, .toggle-serach-leave-to {
    transform: translateX(10px);
    opacity: 0;
  }
  .menu-area{
    z-index: 5;
    top: 56px;
    left: 0px;
    max-width: 480px;
    width: 100%;
    height: 80vh;
    position: absolute;
    background: #333333ee;
    overflow: auto;
  }
  .toggle-menu-enter-active {
    transition: all .3s ease;
  }
  .toggle-menu-leave-active {
    transition: all .3s ease;
  }
  .toggle-menu-enter, .toggle-menu-leave-to {
    transform: translateX(-40vw);
    opacity: 0;
  }
</style>
