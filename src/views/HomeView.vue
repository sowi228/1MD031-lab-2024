<template>
  <header  id="topsection" > 
      <img src="/img/Background.jpg" alt="Header Background">
      <h1>Welcome to O'Donalds</h1>
  </header>   

  <main>
      <section id="burgersection">
          <h2>Menu</h2>
          <p>Select which items to order.</p>
          <div class="wrapper">
            <Burger v-for="burger in burgers"
              v-bind:burger="burger" 
              v-bind:key="burger.name"
              v-on:orderedBurger="addToOrder($event)"/>
          </div>    
      </section>    

      <section id="infosection">
                <h2>Customer information</h2>
                <p>Fill en necessary information.</p>
                
                <form>
                    <fieldset>
                      <h3>Delivery information</h3>
                        <p>
                            <label for="fullname">Full name:{{fn}}</label><br>
                            <input type="text" id="fullname" v-model="fn" required placeholder="First and Last name">
                        </p>
                        <p>
                            <label for="email">E-mail:{{em}}</label><br>
                            <input type="email" id="email" v-model="em" required placeholder="E-mail address">
                        </p>
                        <!--<p>
                            <label for="streetname">Street name:{{sn}}</label><br>
                            <input type="text" id="streetname" v-model="sn" required placeholder="Name of street">
                        </p>
                        <p>
                            <label for="housenumber">House number:{{hnr}}</label><br>
                            <input type="number" id="housenumber" v-model="hnr" placeholder="House address">
                        </p>-->
                        <p>
                            <label for="payment">Payment:</label><br>
                            <select id="payment" v-model="pay">
                                <option value="credit_card">Credit card</option>
                                <option value="swish">Swish</option>
                                <option value="bill">Bill</option>
                                <option value="part_payment">Part payment</option>
                            </select>
                        </p>
                        <p> 
                            Select gender:
                        </p>
                        <p>
                            <input type="radio" id="undisclosed" v-model="gender" value="undisclosed" checked>
                            <label for="undisclosed">Undisclosed</label>
                        </p>
                        <p>
                            <input type="radio" id="female" v-model="gender" value="female">
                            <label for="female">Female</label>
                        </p>
                        <p>
                            <input type="radio" id="male" v-model="gender" value="male">
                            <label for="male">Male</label>
                        </p>               
                    </fieldset>
                    <section id="container">
                      <div id="target"  v-on:click="setLocation($event)">
                        <div
                          v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">
                          T
                          </div>
                      </div>
                    </section>  
                      <button v-on:click="placeOrder">
                        <img src="https://cdn3d.iconscout.com/3d/premium/thumb/send-button-3d-icon-download-in-png-blend-fbx-gltf-file-formats--sending-pack-user-interface-icons-7520519.png?f=webp" 
                            alt="Place order"
                            style="width:20px"/>
                            Place order
                      </button>
                </form>   
            </section> 
        </main>
        <footer>
            <hr>&copy;2024 O'Donalds
        </footer>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import menu from '../assets/menu.json'
import io from 'socket.io-client'

const socket = io("localhost:3000");

//function MenuItem (name, image, kCal, gluten, lactose) {
//  this.name=name;
//  this.img=image;
//  this.kCalCount=kCal;
//  this.gluten=gluten;
//  this.lactose=lactose;
//};

//const theBurgers = [
//  new MenuItem("O'cheese", "https://s7d1.scene7.com/is/image/mcdonalds/mcd-sv-products-burgers-bigmac-NY2:product-header-desktop?wid=829&hei=455&dpr=off", 500, true, true),
//  new MenuItem("O'vegan","https://s7d1.scene7.com/is/image/mcdonalds/mcd-sv-products-burgers-veggie-mcvegan-NEW:1-3-product-tile-desktop?wid=829&hei=515&dpr=off", 400, true, false),
//  new MenuItem("O'sallad", "https://mcdonalds.com.mt/wp-content/uploads/2024/06/CAESAR-CRISPY-CHICKEN-SALAD-WEBSITE-IMG.jpg", 300, false, false),
//];
//console.log(theBurgers);


const theBurgers = menu;

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: theBurgers,
      fn:'',
      em:'',
      //sn:'',
      //hnr:'',
      pay: 'credit_card',
      gender:'',
      orderedBurgers: {},
      location: { x: 0, y: 0}
    }
  },
 
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },
    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top
                    };
      this.location.x = event.clientX - 10 - offset.x
      this.location.y = event.clientY - 10 - offset.y
    },
    placeOrder: function () {
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: this.location.x,
                                            y: this.location.y, 
                                            orderName: this.fn,
                                            orderEmail: this.em,
                                            orderPay: this.pay,
                                            orderGender: this.gender
                                          },
                                orderItems: this.orderedBurgers
                              });
    }
  }
}
</script>

<style>

  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
    font-family:arial, sans-serif;
    font-size: 12pt;

}

p {
    color: red;
    padding-left: 10px;
}

h1 {
    font-family: 'Agbalumo';
    font-size: 36pt;   
}

main, header, footer {
    max-width: 50rem;
    margin: 0 auto;
}

header img {
    overflow: hidden;
    width:100%;
    height:200px;
    opacity: 0.5;
}

header h1 {
    width:40rem;
    margin-top:30px;
    text-align: center;
    position:absolute;
    top:10%;
}

#burgersection {
    background-color: black;
    color:white;
    margin: 10px 0px;
    padding: 1em;   
    border: 10px double #ff0000
}

#infosection {
    background-color: white;
    color:black;
    margin: 10px 0px;
    padding: 1em;
    border: 10px double #ff0000
}

button {
    margin:10px;
}

button:hover {
    background-color:gray;
    cursor:pointer;
 }

 .wrapper {
    display:grid;
    grid-gap:10px;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
 }


 #container {
  margin-top: 20px;
  width: 100%;
  height: 300px;
  overflow: scroll;
 }

 #target {
  position: relative;
  margin: 0;
  padding: 0;
  background-repeat: no-repeat;
  width:1920px;
  height: 1078px;
  cursor: crosshair;
  background-image: url('/img/polacks.jpg');
 }
  
 #target div {
  position: absolute;
  background: black;
  color: white;
  border-radius: 10px;
  width:20px;
  height:20px;
  text-align: center;
 }

</style>