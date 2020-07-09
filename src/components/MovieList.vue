<template>
  <div class="list-movie">
    <!-- Mylist -->
    <h3>Mylist</h3>
    <ul>
      <li v-for="mylist of mylists" :key="mylist.index" @mouseover="mylistOver(mylist.id)" @mouseout="mylistOut(mylist.id)">
        <img :src="mylist.img" :alt="mylist.title">
        <h5>{{mylist.id}}. {{mylist.title}}</h5>
       <div class="btn_ar"> <b-button style="opacity:0;" variant="danger" class="movie-list"  :id="'remove_'+mylist.id" @click="removeList(mylist)" >Remove</b-button></div>
      </li>
      <li v-if="!mylists.length" class="no-record">No Records found in Mylist</li>
    </ul>
    <!-- Recommendation -->
    <h3>Recommendation</h3>
    <ul>
      <li v-for="recommendation of recommendations" :key="recommendation.index" @mouseover="recommendationOver(recommendation.id)" @mouseout="recommendationOut(recommendation.id)">
        <img :src="recommendation.img" :alt="recommendation.title">
       <h5> {{recommendation.id}}. {{recommendation.title}}</h5>
        
        <div class="btn_ar"> 
         <b-button style="opacity:0;" variant="success" class="movie-list" :id="'add_'+recommendation.id" @click="addRecommendation(recommendation)">Add</b-button>
         </div>
      </li>
      <!-- if there is no records for recommendations -->
      <li v-if="!recommendations.length" class="no-record">No Records found in Recommendations</li>
    </ul>

    <!-- Titles of Mylist -->
    <ul class="titles-list">
      <h3>Titles of Mylist</h3>
      <br>
      <li v-for="mylist of mylists" :key="mylist.index">
        {{mylist.id}}.
        {{mylist.title}}
      </li>
      <li v-if="!mylists.length" class="no-record">No Titles found in Mylist</li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";
const recommendationsUrl = "http://localhost:3000/recommendations";
const mylistUrl = "http://localhost:3000/mylist";

export default {
  name: "MovieList",
  props: {
    msg: String,
  },
  data() {
    return {
      recommendations: [],
      mylists: [],
      listdummyObject: {},
    }
  },
  
  methods:{
    // Mouse hover to mylist
    mylistOver(id) {
      const removeId = "remove_"+id;
      document.getElementById(removeId).style.opacity = "1";
    },
    // Mouse out to mylist
    mylistOut(id) {
      const removeId = "remove_"+id;
      document.getElementById(removeId).style.opacity = "0";
    },
    // Remove recrod from mylist
    async removeList(list) {       
      try {
        await axios.delete(`${mylistUrl}/${list.id}`);
        this.mylists = this.mylists.filter((item) => item.id !== list.id);
      }
      catch(e) {
        console.error(e);
      }
    },
    // Mouse hover to recommendations
    recommendationOver(id) {
      const addId = "add_"+id;
      document.getElementById(addId).style.opacity = "1";
    },
    recommendationOut(id) {
       const addId = "add_"+id;
       document.getElementById(addId).style.opacity = "0";
    },
    // Remove recrod from recommendations and push to Mylist
    async addRecommendation(recommendList) {
      this.listdummyObject.title = recommendList.title;
      this.listdummyObject.img = recommendList.img;
      try {
       // Pushing from recomdation lists to my list
       const res = await axios.post(mylistUrl, this.listdummyObject);
       this.mylists = [...this.mylists, res.data];
        // After push , delete the record from recommendations
        await axios.delete(`${recommendationsUrl}/${recommendList.id}`);
        this.recommendations = this.recommendations.filter((item) => item.id !== recommendList.id);
      }
      catch(e) {
        console.error(e);
      }
    },
  },
  computed: {
   
  },

  async created() {
    try{
      // recommendations
      const res = await axios.get(recommendationsUrl);
      this.recommendations = res.data;
      // mylist
      const responseList = await axios.get(mylistUrl);
      this.mylists = responseList.data;
    }
    catch(e){
      console.error(e); 
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 10px;
  color: #191919;
  font-size: 18px;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 700;
  position: relative;
  margin-bottom: 25px;
}
h3:before {
  content: "";
  position: absolute;
  width: 37px;
  height: 3px;
  background: #dc3545;
  bottom: -4px;
  left: 0;
  right: 0;
  margin: auto;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  background: #fff;
  margin: 0px 10px;
  width: 200px;
  box-shadow: 0px 1px 10px #ddd;
  img{
    vertical-align: middle;
    border-style: none;
    width: 100%;
    object-fit: cover;
    //height: 200px;
    cursor: pointer;
  }
  h5{
    color: #000;
    font-weight: 600;
    font-size: 16px;
    margin-top: 12px;
    margin-bottom: 10px;
    }
}
a {
  color: #42b983;
}
ul.titles-list {
h3{
  margin-bottom: 0px;
 }
li{
  background: none;
  box-shadow: none;
  width: auto;
 }
}
button.movie-list {
  width: 90px;
  border-radius: 2px;
  padding: 5px 12px;
  font-size: 13px;
}
.btn_ar{
  padding-bottom: 15px;
}
li.no-record{
  background: none;
  box-shadow: none;
  color: #dc3545;
  width: auto;
}
</style>
