<template>
  <div class ="wrapper">
    <Burger v-for = "burger in burgers"
            v-bind:burger="burger"
            v-bind:key="burger.name"
            v-bind:src="burger.url"/>
      <div class="box">
        <h3>{{burger.name }} <span id = amountOrdered>{{ amountOrdered }}</span></h3>
        <img v-bind:src="burger.url" style="width: 200px">
        <ul>
          <li> {{ burger.kCal }} kCal </li>
          <li v-if="burger.gluten">Contains <span class="ingredients">gluten</span></li>
          <li v-if="burger.lactose">Contains <span class="ingredients">lactose</span> </li>
          <li v-else>Contains <span class="ingredients">nuts</span></li>
        </ul>
        <div>
          <button class = "burgerButtons" v-on:click="increaseBurgers">
            <img id = "plusImage" src="https://cdn.icon-icons.com/icons2/2550/PNG/512/plus_icon_152556.png">
          </button>
          <div>
            <button class="burgerButtons" v-on:click="decreaseBurgers">
              <img id = "minusImage" src="https://cdn0.iconfinder.com/data/icons/typicons-2/24/minus-1024.png">
            </button>
          </div>
        </div>
      </div>

  </div>
</template>

<script>
export default {
  name: 'Burger',
  props: {
    burger: Object,
  },
  data: function () {
    return {
      amountOrdered: 0,
    }
  },
  methods: {
    increaseBurgers: function() {
      this.amountOrdered++;
      this.$emit('orderedBurger', { name:   this.burger.name,
            amount: this.amountOrdered
          }
      );
    },
    decreaseBurgers: function(){
      if(this.amountOrdered > 0) {
        this.amountOrdered--;
        this.$emit('orderedBurger', { name:   this.burger.name,
              amount: this.amountOrdered
            }
        );
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.ingredients {
  font-weight: bold;
}
.box {
  border-radius: 50px;
  padding: 1px;
  border: 2px solid blue;
  color:white;
}
.box > h3 {
  padding-left: 3em;
  color: navajowhite;
}
#amountOrdered {
  color: darkred;
}
.burgerButtons{

}
#plusImage{
  height:20px;
  width:20px;
  background-color:blue;
}
#minusImage{
  height:20px;
  width:20px;
  background-color:blue;
}
</style>

