<template>
  <div class="vote-component" :class="[ isLike ? 'like' : 'dislike', isActive ? 'active' : 'inactive']" @click="clicked()">
    <img src="@/assets/img/thumbs-up.svg" alt="thumbs down" v-if="isLike"/>
    <img src="@/assets/img/thumbs-down.svg" alt="thumbs down" v-if="!isLike"/>
  </div>
</template>

<script>

export default {
  name: 'Vote',
  props: {
    isLike: { type:Boolean , default: true },
    isButton: { type:Boolean, default: false },
    isActive: { type:Boolean, default: false }
  },
  methods:{
    clicked(){
      if(this.isButton)
        this.$emit('clicked', this.isLike);
    }
  }
}
</script>

<style>
.vote-component{
  width: 40px;
  height: 40px;
  display: flex;
  transition: ease-in-out 0.2s all;
}
.vote-component img{
  display:block;
  margin:auto;
  scale: 1.4;
}
.like{
  background-color: rgba(var(--color-green-positive), 1);
  opacity: 0.75;
}
.like:hover{
  opacity: 1;
}
.like:hover[disabled]{
  opacity: 0.75;
}
.dislike{
  background-color: rgba(var(--color-yellow-negative), 1);
  opacity: 0.75;
}
.dislike:hover{
  opacity: 1;
}
.dislike:hover[disabled]{
  opacity: 0.75;
}
.active{
  border: 2px solid black;
  opacity: 1;
}
.inactive{
  border: 2px solid rgba(1,1,1,0);
}
</style>