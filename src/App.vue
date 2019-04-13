<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld style="display: none;" msg="Welcome to Your Vue.js App" />
    <form @submit.prevent>
      <div id="card-number"></div>
      <div id="card-cvv"></div>
      <button :disabled="isPayButtonDisabled">Pay</button>
    </form>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
/* eslint-disable */
export default {
  name: "app",
  components: {
    HelloWorld
  },
  data() {
    return {
      fattjs: null,
      isPayButtonDisabled: false
    };
  },
  computed: {
    FattJS() {
      return window.FattJs;
    }
  },
  created() {
    const self = this;
    console.log(this.FattJs);
    self.fattjs = new self.FattJS("ahmadkayyali-apisandbox", {
      //attrs for the CC number field
      number: {
        //id of the div to contain the CC num field
        id: "card-number",
        // placeholder the field should contain
        placeholder: "1234 5678 9876 5432",
        // styles to apply to corresponding field
        style: "height: 30px; width 100%; font-size: 15px;"
      },
      cvv: {
        // id for the CVV field
        id: "card-cvv",
        // placeholder of the field
        placeholder: "CVV",
        // Styles
        style: "height: 30px; width: 100%; font-size: 15px;"
      }
    });

    // Load the card forms now that we have everything place
    self.fattjs
      .showCardForm()
      .then(handler => {
        console.log("form was loaded");
        // Quick test with test nums
        handler.setTestPan("4111111111111111");
        handler.setTestCvv("123");
      })
      .catch(err => {
        console.log("Error loading form: ", err);
      });

    // Handlers for form copmletion
    // KEEP THESE IN THE LIFECYCLE METHOD
    let disabled = "";
    // INVALID
    self.fattjs.on("card_form_uncomplete", message => {
      //Message displays if form is incomplete or invalid
      console.log(message);

      // Update pay button and force it to prevent error
      self.isPayButtonDisabled = true;
      self.$forceUpdate();
    });

    // VALID
    self.fattjs.on("card_form_complete", message => {
      // Fields are valid
      console.log(message);

      // Update pay button and force it to prevent error
      self.isPayButtonDisabled = false;
      self.$forceUpdate();
    });
  },
  methods: {
    handleFormInputs: function() {
      const self = this;
    }
  },
  updated() {
    console.log(this.isPayButtonDisabled);
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#card-number {
  height: 35px;
  width: 100%;
  font-size: 15px;
  font-family: Helvetica Neue, Helvetica;
  color: #31325f;
  font-weight: 300;
}

#card-cvv {
  height: 35px;
  width: 100%;
  font-size: 15px;
  font-family: Helvetica Neue, Helvetica;
  color: #31325f;
  font-weight: 300;
}

#card-cvv,
#card-number {
  /* border: 1px solid black; */
}
</style>
