<template>
  <header>
    <div class="navbar navbar-dark bg-dark sticky-top">
      <button class="navbar-toggler text-light p-0 border-0" type="button" data-toggle="collapse" data-target="#navbarToggleExternalContent" aria-controls="navbarToggleExternalContent" aria-expanded="false" aria-label="Toggle navigation">
        <i class="material-icons m-1">toc</i>
      </button>
      <a class="navbar-brand text-light float-left" href="/">Nicoview</a>

      <!-- Button trigger modal -->
      <button type="button" class="btn text-light p-0" @click="toggleOpen">
        <i class="material-icons m-1">search</i>
      </button>

      <div class="input-group mt-3 mb-1" v-show="opened">
        <!-- 検索ボックス -->
        <select class="px-2" name="" id="search_mode">
          <option value="">検索</option>
          <option value="&targets=tagsExact">tag検索</option>
        </select>

        <input type="text" class="form-control" placeholder="serach:" aria-label="sss" aria-describedby="basic-addon2" id="serach_box" @keydown.enter="entered">
        <div class="input-group-append">
          <button class="btn btn-info" type="button" @click="entered">検索</button>
        </div>
      </div>

    </div>

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
      opened:false
    }
  },
  methods:{
    toggleOpen: function(){
      this.opened=!this.opened;
    },
    focusSerachBtn: function(){
      if(this.opened){
        document.getElementById("serach_box").focus();
      }
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

</style>
