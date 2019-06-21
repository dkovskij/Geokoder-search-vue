<template>
  <div id="app">
    <input v-model="query" @input="debouncedSearch" type="text" placeholder="Поиск по адресу" id="searchInput"> 
    <div class="content">              
      <div v-if='matched.length > 0' class="search-wrap">
        <div v-for="item in matched" class="matched-item">
          <div>
            {{item}}
          </div>
        </div>
      </div>
      <div v-else class="not-found">
        Ничего не найдено
      </div>
  </div>  
  </div>
</template>

<script>
import 'es6-promise/auto'
import debounce from 'lodash/debounce';
import axios from 'axios';

export default {
  data() {
    return {
      name: 'App', 
      ans: [],
      matched: [],
      query: ''    
    }    
  },
  methods: {    
    debouncedSearch: debounce(function () {            
      let x = this     
      axios.get(`https://geocode-maps.yandex.ru/1.x/?apikey=412c36af-379b-492e-b855-e1b6700f002f&format=json&geocode=${this.query}&results=5`)
     .then(function(response) {
       x.ans = response.data.response.GeoObjectCollection.featureMember  
       x.matched = [];
       x.ans.forEach(el => (x.matched.push(el.GeoObject.name)))       
     })
     .catch(function (error) {        
       console.log(error)   
     })  
    }, 500),    
  },
};

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#searchInput {
  width: 300px;
  height: 30px;
  border-radius: 10px;
  border: 1px solid grey;
  padding-left: 20px;
}
.search-wrap {
  border: 1px solid #9e9e9e;
  width: 300px;  
  margin: 0 auto;
  border-radius: 10px;
  box-shadow: 0 0 10px #9e9e9e;
}
.matched-item {
  cursor: pointer;
  height: 30px;
  line-height: 30px;
}
.matched-item:hover {
  background-color: #ececec;
}
.not-found {
  margin-top: 20px;
}
</style>
