<template>

  <div id="Header">
    <img id="HeaderImage" src="https://www.freewpheaders.com/wp-content/gallery/food-gallery/burger-sandwich-header.jpg">
    <header id="HeaderText">
      <h1>Welcome to BurgerOnline</h1>
    </header>
  </div>
  <main>
    <section id="BurgerInformation">
      <div>
        <h2>Select burger</h2>
        <p>This is where you execute burger selection</p>
      </div>
      <div class="wrapper">
          <Burger v-for="burger in burgers"
                  v-bind:burger="burger"
                  v-bind:key="burger.name"
                  v-bind:src="burger.url"
                  v-on:orderedBurger="addToOrder($event)"
          />
      </div>
    </section>
    <section id="CustomerInformation">
      <div>
        <h2>Customer information</h2>
        <p> This is where you provide necessary information</p> <!-- Kustomer information -->
      </div>
      <div id="contact">
        <h3>
          Delivery information
        </h3>
        <form>
          <p>
            <label for="fullname">Full name</label><br>
            <input type="text" id="fullname" v-model="fn" required="required" placeholder="First- and last name">
          </p>
          <p>
            <label for="email">E-mail</label><br>
            <input type="email" id="email" v-model="em" required="required" placeholder="E-mail address">
          </p>

      <div>
        <p>
          <label for="PaymentMethod">Payment method</label><br>
          <select id="PaymentMethod" v-model="rcp">
            <option selected="selected">Swish</option>
            <option>Klarna</option>
            <option>Bank transfer</option>
            <option>Cash</option>
          </select>
        </p>
      </div>
      <div>
        <p>
          <label>Gender</label><br>
          <input type="radio" id="Male" v-model="gender" value="male" checked="checked">
          <label>Male</label><br>
          <input type="radio" id="Female" v-model="gender" value="female">
          <label>Female</label><br>
          <input type="radio" id="Not provided" v-model="gender" value="Do not wish to provide">
          <label>Do not wish to provide</label>
        </p>
      </div>
        </form>
      </div>
      <div id="mapContainer">
        <div id="map" v-on:click="setLocation">
          <div id="dots">
            <div v-bind:style="{ left: location.x + 'px',
            top: location.y + 'px' }">
              T
            </div>
          </div>
        </div>
      </div>
    </section>
    <div>
      <button type="submit" v-on:click="markDone">
        <img id = "ButtonImage" src="https://thumbs.dreamstime.com/b/shopping-cart-icon-trolley-icon-shopping-cart-icon-trolley-icon-vector-illustration-isolated-white-background-163727286.jpg">
        Place order
      </button>
    </div>
  </main>
  <hr>
  <footer>
    &copy; 2021 Simon Pettersson Palestro
  </footer>
</template>

<script>
import Burger from '../components/Burger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

function MenuItem(name, url, kCal, lactose, gluten) {
  this.name = name;
  this.url = url;
  this.kCal = kCal;
  this.lactose = lactose;
  this.gluten = gluten;
}

const BurgerArray = [new MenuItem("Big Chicken", "https://img.freepik.com/free-photo/burger-black-background-menu_127425-580.jpg?size=626&ext=jpg", 1500, true, false),
  new MenuItem("Big Tasty", "https://thumbs.dreamstime.com/b/fresh-tasty-burger-black-background-appetizing-cheeseburger-meat-juicy-117403538.jpg", 1200, false, true),
  new MenuItem("Big Nasty", "https://media.istockphoto.com/photos/burger-on-black-background-picture-id1240080543?k=20&m=1240080543&s=170667a&w=0&h=KHJlJmQepe4UFUIHw91-R33rdwjmpJBaQMDyAAhCHtQ=", 1000, true, true)];

console.log(BurgerArray)

export default {
  name: 'Home',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      fn:"",
      em:"",
      st:"",
      hou:"",
      rcp:"Swish",
      gender:"Male",
      orderedBurgers:{},
      location: { x: 0,
        y: 0
      },
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
   /*  addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      console.log(this.location.x = event.clientX - 10 - offset.x,
          this.location.y = event.clientY - 10 - offset.y,),
      this.location.x = event.clientX - 10 - offset.x;
      this.location.y = event.clientY - 10 - offset.y;
    },*/
    setLocation: function(event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      this.location.x = event.clientX - 10 - offset.x;
      this.location.y = event.clientY - 10 - offset.y;

    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },
    markDone: function() {
      console.log(this.em,this.fn, this.rcp, this.gender, this.orderedBurgers)
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
            details: { x: this.location.x, y: this.location.y,
              fn:this.fn, em:this.em, rcp:this.rcp, gender:this.gender},
            orderItems: [this.orderedBurgers]
          }
      );
    },
  }
}
</script>

<style>

#mapContainer{
  height: 500px;
  width: 700px;
  overflow:scroll;
}

  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
  }
  body {
    font-family: "Times New Roman", sans-serif ;
  }
  button:hover {
    cursor:pointer;
    color: white;
    background: blue;
  }
  section {
    margin: 1em;
  }
  #BurgerInformation > div {
    padding-left: 1em;
    padding-right: 1em;
  }
  #BurgerInformation{
    border: 2px dashed white;
    color: white;
    background: black;
  }
  #CustomerInformation{
    border: 2px dashed black;
    color: black;
  }
  #CustomerInformation > div {
    padding-left: 1em;
  }
  #Header{
    padding-right: 1em;
    padding-left: 1em;
    height:10em;
    overflow:hidden;
  }
  #HeaderImage{
    opacity:0.6;
    height:auto;
    width:100%;
  }
  #HeaderText{
    position:absolute;
    margin-top:-20em;
    margin-left: 1em;
    color: navajowhite;
  }
  .wrapper {
    display: grid;
    grid-gap: 30px;
    grid-template-columns: 205px 205px 205px;
    color: #444;
  }
  #ButtonImage {
    width: 20px;
    height: 20px;
  }
/*#dots {
  position: relative;
  margin: 0;
  padding: 0;
  background: url(/img/polacks.jpg);
  background-repeat: no-repeat;
  width:1920px;
  height: 1078px;
  cursor: crosshair;
}*/
#dots div {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width:20px;
  height:20px;
  text-align: center;
}
</style>
