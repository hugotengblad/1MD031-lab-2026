<template>
<header>
            <img src="/img/restaurang.jpg" id="header-img" alt="Restaurang">
            <h1 >Välkommen till Bobr Burger!</h1>
        </header>


        <main>
        <section id="menu">
            <h2 id="slogan">Hungrig? Varsågod att damma!</h2><br>
            <h2 id="menurubrik">Vår meny:</h2>


            <div class="wrapper">

              <OneBurger v-for="burger in menu"
              :key="burger.name"
              :burger="burger"
              v-on:ordered-burger="addToOrder($event)" />

            </div>


        </section>

        <section id="orderback">
            <h2>Leveransinformation</h2>

                <div class="map-container">
                  <div id="map" v-on:click="setLocation">
                      <div id="dots">
                        <div v-bind:style="{ left: location.x + 'px', top: location.y + 'px' }">
                          T
                        </div>
                     </div>
                  </div>
                </div>


                <p>
                    <label for="name">Fullständigt namn</label><br>
                    <input type="text" id="name" v-model="fullName" required="required" placeholder="Fullständigt namn">
                  </p>


                <p>
                     <label for="email">E-mail</label><br>
                    <input type="email" id="email" v-model="email" required="required" placeholder="E-mail address">
                </p>

                <p>
                    <label for="payment">Betalning</label><br>
                    <select id="payment" v-model="payment">
                        <option selected="selected">Kreditkort</option>
                        <option>Klarna</option>
                        <option>Swish</option>
                        <option>Springnota</option>
                    </select>
                </p>

                <p>
                    <span id="gender">Kön</span><br>
                    <input type="radio" id="gender" v-model="gender" value="woman">
                    <label for="woman">Kvinna</label><br>
                    <input type="radio" id="gender" v-model="gender" value="man">
                    <label for="man">Man</label><br>
                    <input type="radio" id="gender" v-model="gender" value="nonbinary">
                    <label for="nonbinary">Icke-binär</label><br>
                    <input type="radio" id="gender" v-model="gender" value="undisclosed" checked="checked">
                    <label for="undisclosed">Vill ej ange</label><br>
                </p>

                <button type="button" v-on:click="placeOrder">
                    <img src="/img/bestick.jpg" width="14">
                    Beställ din burgare!
                </button>

        </section>
        </main>
        <hr>
        <footer>
            <p id="footer"> &copy; 2026 Bobr Burger</p>
        </footer>
</template>

<script>
import OneBurger from '../components/OneBurger.vue';
import menu from '../assets/menu.json';
import io from 'socket.io-client'

const socket = io("localhost:3000");

function MenuItem(name, description, content, g, url, gluten, lactose) {
  this.name = name;
  this.description = description;
  this.content = content;
  this.g = g;
  this.url = url;
  this.gluten = Boolean(gluten);
  this.lactose = Boolean(lactose);
}

const burgers = [
  new MenuItem("SuperSmash", "Smash or pass? Är det ens en fråga?","Bacon, ost, sallad, Bobr dressing", 180, "img/hamburgare_1.jpg", true, true),
  new MenuItem("Bun Apetite","Säg mig en burgare med bättre bröd", "Ost, picklad rödlök, majo", 180, "img/hamburgare_2.jpeg", false, false),
  new MenuItem("Bobr Burger", "Klarar-du den?", "Kött och ost, inget mer", 450, "img/hamburgare_3.jpeg", true, false)
];

console.log(burgers);

export default {
  name: 'HomeView',
  components: {
    OneBurger
  },
  data: function () {
    return {
      menu: menu,
      orderedBurgers: {},
      location: { x: 0, y: 0 },
      fullName: "",
      email: "",
      payment: "Kreditkort",
      gender: "",
    }
  },
  methods: {
    placeOrder: function () {
    console.log(
      this.fullName,
      this.email,
      this.payment,
      this.gender,
      this.orderedBurgers
    );

    socket.emit("addOrder", { orderId: this.getOrderNumber(),
      details: {
        x: this.location.x,
        y: this.location.y
      },
      orderItems: this.orderedBurgers,
      personal: {
        fullName: this.fullName,
        email: this.email,
        payment: this.payment,
        gender: this.gender,
      }
    });
  },

    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },

    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      /*socket.emit("addOrder", { orderId: this.getOrderNumber(), */
      this.location = {
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y
      };

    }

  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
body {
    font-size: 12pt;
    font-family: Georgia, 'Times New Roman', Times, serif;

}


p {
    color: white;
}

main, header, footer, nav ul {
    max-width: 100%;
    margin: 0 auto 0 auto;
}
main {
    background-color: white;
}

section {
    padding: 20px;
    border: 2px dashed white;
}

div {
    margin: 1vw;
    padding: 1vw;
}

img {
    max-width: 100%;
    height: auto;
}

/* nav ul li {
    display: inline-block;
    background-color: grey;
    padding: 1em;
    margin: 1em;
} */

header {
    position: relative;
    /*background-image: linear-gradient(rgba(255, 255, 255, 0.3), rgba(255, 255, 255, 0.3)), url("/img/restaurang.jpg");
    background-size: cover;*/
    overflow: hidden;
    width: 100%;
    height: 250px;
}

#header-img {
    width: 100%;
    height: 100%;
    opacity: 0.5;
}

h1 {
    font-family: 'Agbalumo';
    font-size: 36pt;
    color: black;
}

header h1 {
    position: absolute;
    top: 20px;
    left: 0;
    width: 100%;
    margin: 0 auto;
    text-align: center;
    color: black;
}

nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: grey;
    padding: 1em;
}

.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: bold;
}

#allergi {
   font-weight: bold;
}

#slogan {
    color: white;
    font-family: 'Agbalumo';
    font-size: 30px;
    margin-left: 1vw;
}

#menurubrik {
    font-size: 30px;
    margin-left: 1vw;
}

#menu {
    background-color: black;
    color: white;
}

.contains {
  color: lightcyan;
  font-size: small;
}

label {
    color: black;
}

button {
    margin: 0 auto 20px auto;
    background-color: white;
}

option:hover, input:hover, select:hover, button:hover {
    background-color: pink;
    cursor: pointer;
}


.wrapper {
     display: grid;
     grid-gap: 1px;
     grid-template-columns: repeat(3,1fr);
     background-color: grey;
     color: black;
 }

 #gender {
    color: black;
 }

 .map-container {
    width: 100%;
    height: 300px;
    overflow: scroll;
 }
 #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
  }

  #footer {
    color: black;
  }

</style>
