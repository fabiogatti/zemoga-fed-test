<template>
  <div class="card-component-list">
    <div class="background flex-row">
      <div class="photo-div">
        <img :src="getImgUrl" :alt="card.name">
      </div>
    </div>
    <vote class="current-status" :isLike='card.likes >= card.dislikes ? true : false' disabled></vote>
    <div class="time-ago">
      <p v-show="!voted">{{ yearAgoText + card.category }}</p>
      <p v-show="voted">Thank you for your vote!</p>
    </div>
    <div class="card-content">
      <div class="top-name">
        <h1>{{ card.name }}</h1>
      </div>
      <div class="middle-desc flex-row">
        <div class="description">
          <p>{{ trimmedDesc }}</p>
        </div>
        <div class="buttons-div flex-row">
          <vote class="btn" :isLike='true' :isButton="true" :isActive="clickedUpvote" @clicked='clickedVote' v-show="!voted"></vote>
          <vote class="btn" :isLike='false' :isButton="true" :isActive="clickedDownvote" @clicked='clickedVote' v-show="!voted"></vote>
          <button class="btn btn-vote" @click="submitVote()" :class="[ clickedUpvote || clickedDownvote ? 'btn-active' : 'btn-inactive']">{{ voteBtnText }}</button>
        </div>
      </div>
      <div class="bottom-votes flex-row">
        <div class="up" :style="{ width : likesPercentage+'%' }"  :key="voted+totalPositiveVotes+'1'">
          <div class="flex-row">
            <img src="@/assets/img/thumbs-up.svg" alt="thumbs up"/>
            <h1 :key="voted+totalPositiveVotes">{{ likesPercentage }}%</h1>
          </div>
        </div>
        <div class="down justify-flex-end" :style="{ width : dislikesPercentage+'%' }" :key="voted+totalNegativeVotes+'1'">
          <div class="flex-row">
            <h1 :key="voted+totalNegativeVotes">{{ dislikesPercentage }}%</h1>
            <img src="@/assets/img/thumbs-down.svg" alt="thumbs down"/>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { CardClass } from '@/classes/CardClass';
import Vote from './Vote.vue';

export default {
  name: 'Card',
  components: { Vote },
  props: {
    card: CardClass,
    isGrid: Boolean
  },
  data() {
    return {
      clickedUpvote: false,
      clickedDownvote: false,
      voted: false,
      storageNegatives: Number,
      storagePositives: Number
    }
  },
  computed: {
    yearAgoText(){
      const lastDate = new Date(this.card.lastUpdated);
      const now = new Date();
      var diffTime = Math.abs(now - lastDate);
      var diffDays = Math.floor(diffTime / (1000 * 60 * 60 * 24));
      if(diffDays==0){
        return 'less than a day ago in ';
      }
      else if(diffDays>365){
        var years = Math.floor(diffDays/365)
        if(years == 1)
          return '1 year ago in ';
        return years.toString() + ' years ago in ';
      }
      else if(diffDays>30){
        var months = Math.floor(diffDays/30)
        if(months == 1)
           return '1 month ago in ';
        return months.toString() + ' months ago in ';
      }
      else{
        if(diffDays == 1)
          return '1 day ago in ';
        return diffDays.toString() + ' days ago in ';
      }
    },
    totalPositiveVotes(){
      return this.card.likes + this.storagePositives;
    },
    totalNegativeVotes(){
      return this.card.dislikes + this.storageNegatives;
    },
    totalVotes(){
      return this.totalPositiveVotes + this.totalNegativeVotes;
    },
    likesPercentage(){
      return Math.ceil(this.totalPositiveVotes/this.totalVotes*100);
    },
    dislikesPercentage(){
      return Math.floor(this.totalNegativeVotes/this.totalVotes*100);
    },
    voteBtnText(){
      if(this.voted){
        return 'Vote Again';
      }
      else
        return 'Vote Now'
    },
    trimmedDesc(){
      if(this.card.description.length < 150)
        return this.card.description
      return this.card.description.substring(0,149) + '...'
    },
    getImgUrl() {
      var images = require.context('../assets/img', false, /\.jpg$/);
      return images('./' + this.card.name + ".jpg");
    }
  },
  methods:{
    storageString(isLike){
      if(isLike)
        return this.card.name+'likes';
      else
        return this.card.name+'dislikes'
    },
    storageVote(isLike){
      var storedLikes = localStorage.getItem(this.storageString(isLike));
      return storedLikes ? parseInt(storedLikes) : 0;
    },
    clickedVote(isLike){
      if(isLike){
        this.clickedUpvote = !this.clickedUpvote;
        this.clickedDownvote = false;
      }
      else{
        this.clickedDownvote = !this.clickedDownvote;
        this.clickedUpvote = false;
      }
    },
    submitVote(){
      if(!this.voted){
        if(this.clickedUpvote){
          localStorage.setItem(this.storageString(true),this.storageVote(true) + 1);
          this.storagePositives += 1;
        }
        else if(this.clickedDownvote){
          localStorage.setItem(this.storageString(false),this.storageVote(false) + 1);
          this.storageNegatives +=1;
        }
        else
          return;
        this.voted = true;
      }
      else{
        this.clickedUpvote = false;
        this.clickedDownvote = false;
        this.voted = false;
      }
        
    }
  },
  mounted(){
    this.storageNegatives = this.storageVote(false);
    this.storagePositives = this.storageVote(true);
  }
}
</script>

<style scoped>
h1{
  margin: 0;
  margin-top: 10px;
  font-weight: 500;
}
*{
  z-index: 1;
  color: var(--color-white);
}
.card-component-list{
  position: relative;
  margin-bottom: 25px;
}
.background{
  position: absolute;
  z-index: 0;
  width: 100%;
  height: 100%;
  background-image: linear-gradient(to right, transparent 0%, var(--color-gradient-light-gray) 10% , var(--color-gradient-dark-gray) 55%, var(--color-gradient-light-gray));
}
.photo-div{
  width:20%;
  height: 100%;
}
.photo-div img{
  height: 100%;
  width: 100%;
  object-fit: cover;
  mask-image: linear-gradient(to right, rgba(0,0,0,1), rgba(0,0,0,0));
}
.current-status{
  position: absolute;
  left: 0;
  top: 0;
}
.time-ago{
  position: absolute;
  top: 0;
  right: 0;
  padding-right: 15px;
}
.up{
  background-color: rgba(var(--color-green-positive), .8);
}
.down{
  background-color: rgba(var(--color-yellow-negative), .8);
}
.card-content{
  display: flex;
  flex-direction: column;
}
.top-name{
  margin-left:20%;
}
.middle-desc{
  margin-left:20%;
}
.description{
  word-break: break-all;
}
.buttons-div{
  align-items: center;
  min-width: 250px;
  justify-content: flex-end;
  margin-right: 10px;
}
.btn{
  margin: 0 5px;
}
.btn-vote{
  width: max-content;
  transition: ease-in-out 0.2s all;
  border: 2px solid var(--color-white);
  color: var(--color-white);
  height: 48px;
  padding: 0 20px;
  font-size: 20px;
  font-weight: 300;
}
.btn-inactive{
  background-color: var(--color-dark-gray); 
}
.btn-active{
  background-color: var(--color-darker-gray); 
}
.bottom-votes h1{
  margin: 10px 0;
  font-weight: normal;
  color: var(--color-white);
  font-size: 30px;
}
.flex-row{
  display: flex;
  flex-direction: row;
}
.justify-flex-end{
  display: flex;
  justify-content: flex-end;
}
.bottom-votes img{
  scale: 1.4;
  padding: 0px 10px;
}
</style>