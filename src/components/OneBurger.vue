<template>
  <!--<div>
    {{ burger.name }} {{ burger.kCal }}
  </div> -->

  <div class="box"
    style="display: inline-block">
    <h3>{{burger.name}}</h3> 
    <img v-bind:src="burger.img" 
        alt="burger.name"
        title="burger.name" 
        style="width:200px; height:150px;">
    <ul>
        <li>Kcal:{{burger.kCalCount}}</li> 
        <li v-if="burger.gluten" class="allergens">Gluten</li> 
        <li v-if ="burger.lactose" class="allergens">Lactose</li> 
    </ul>
    <button v-on:click="increaseAmount">Increase amount of burgers</button>
    <button v-on:click="decreaseAmount">Decrease amount of burgers</button>
    <p> Order: {{ amountOrdered }} </p>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },

  data: function() {
    return{
      amountOrdered:0,
  }
},
methods: {
  increaseAmount: function() {
    this.amountOrdered +=1;
    this.$emit('orderedBurger',{name: this.burger.name, amount: this.amountOrdered});
  },
  decreaseAmount: function() {
    if (this.amountOrdered>0){
      this.amountOrdered -=1;
    }
    this.$emit('orderedBurger', {name: this.burger.name, amount: this.amountOrdered});
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

 .allergens {
    text-transform: uppercase;
    font-weight: bold;
 }

 .box {
 }



</style>