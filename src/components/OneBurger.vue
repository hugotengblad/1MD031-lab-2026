<template>
   <div class="box">
     <h3>{{burger.name}}</h3>
       <p>{{ burger.description }}</p>
          <img v-bind:src="burger.url">


        <ul class="contains">
          <li>{{ burger.g }} gram högrev</li>
          <li>{{ burger.content }}</li>
          <li v-if="burger.gluten">Innehåller <span class="allergi">gluten</span></li>
          <li v-if="burger.lactose">Innehåller <span class="allergi">lactose</span></li>
        </ul>


        <div class="order-controls">
            <p>Lägg till</p>
            <button type="button" v-on:click="decrease" id="decrease">-</button>
            <button type="button" v-on:click="add" id="add">+</button>
            {{ amountOrdered }}
        </div>

  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
  return {
    amountOrdered: 0,
  }
},
methods: {
    add: function () {
      this.amountOrdered += 1;
      this.$emit('ordered-burger', {
        name: this.burger.name,
        amount: this.amountOrdered
        });
    },
    decrease: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1;
      this.$emit('ordered-burger', {
        name: this.burger.name,
        amount: this.amountOrdered
        });
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.allergi {
   font-weight: bold;
}

.contains {
  color: lightcyan;
  font-size: small;
}

 .box {
    background-color: black;
    color: white;
    border-radius: 1px;
    padding: 20px;
    display: flex;
    flex-direction: column;
 }

 .order-controls {
  margin-top: auto;
 }

.order-controls button{
  margin-right: 8px;
}


</style>
