<template>
  <div class="card-component-list">
    <div class="current-status" :class="[ card.likes <= card.dislikes ? 'down' : 'up']">
      <img src="@/assets/img/thumbs-down.svg" alt="thumbs down" v-if="this.card.likes <= this.card.dislikes"/>
      <img src="@/assets/img/thumbs-up.svg" alt="thumbs down" v-if="this.card.likes > this.card.dislikes"/>
    </div>
    <div class="time-ago">
      <p>{{ yearAgoText + card.category }}</p>
    </div>
    <div class="card-content">
      <div class="top-name">
        <h1>{{ card.name }}</h1>
      </div>
      <div class="middle-desc">
        <div class="description">
          <p>{{ card.description }}</p>
        </div>
      </div>
      <div class="bottom-votes flex-row">
        <div class="up" :style="{ width : likesPercentage+'%' }">
          <div class="flex-row">
            <img src="@/assets/img/thumbs-up.svg" alt="thumbs up"/>
            <h1>{{ likesPercentage }}%</h1>
          </div>
        </div>
        <div class="down justify-flex-end" :style="{ width : dislikesPercentage+'%' }">
          <div class="flex-row">
            <h1>{{ dislikesPercentage }}%</h1>
            <img src="@/assets/img/thumbs-down.svg" alt="thumbs down"/>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { CardClass } from '@/classes/CardClass';

export default {
  name: 'Card',
  props: {
    card: CardClass,
    isGrid: Boolean
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
    totalVotes(){
      return this.card.likes + this.card.dislikes;
    },
    likesPercentage(){
      return Math.ceil(this.card.likes/this.totalVotes*100);
    },
    dislikesPercentage(){
      return Math.floor(this.card.dislikes/this.totalVotes*100);
    }
  },
  methods:{

  },
  created(){
    console.log(this.card)
  }
}
</script>

<style scoped>
.card-component-list{
  position: relative;
  min-height: 200px;
  height: 30vh;
  margin-bottom: 25px;
}
.current-status{
  position: absolute;
  left: 0;
  top: 0;
  width: 40px;
  height: 40px;
  display: flex;
}
.time-ago{
  position: absolute;
  top: 0;
  right: 0;
}
.up{
  background-color: rgba(var(--color-green-positive), .8);
}
.down{
  background-color: rgba(var(--color-yellow-negative), .8);
}
.current-status img{
  display:block;
  margin:auto;
  scale: 1.4;
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
.bottom-votes{
  min-height: ;
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