<template>
  <div>
    <div class="container">
      <a href="https://nyucoins.com"><img class="logo" src="@/assets/NYU_Coins_logo.svg" alt="logo"></a>

        <form @submit.prevent="handleSubmitForm">
          <div class="form-group">
            <label for="crypto">Crypto:</label>
          
            <div class="form-group-inner">
              <input type="text" class="crypto" v-model="crypto.name" placeholder="BTC" required>
              <button class="add">Add</button>
            </div>
            <p class="info">Use Coin Abbreviation</p>

          </div>
        </form>

        <div class="crypto-container">
          <div class="crypts" v-for="(item, name) in CryptosList" :key="item._id">
            <p class="price">${{ formatPrice(item.USD) }}</p>
            <div class="lower">
              <p class="crypto-label">{{ name }}</p>
              <button class="delete-btn" @click.prevent="deleteCrypto(name)"><img src="@/assets/trash.png" alt="delete crypto"></button>
            </div>
          </div>
        </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    data() {
      return {
        baseApiURL: 'http://localhost:4000/api',
        crypto: {
          name: ''
        },
        Cryptos: [],
        CryptosList: {}
      }
    },
    created() {
      this.getCryptos()
    },
    methods: {
      getCryptos() {
        axios.get(this.baseApiURL).then(res => {
          this.Cryptos = res.data;

          let names = Array.prototype.map.call(this.Cryptos, s=>s.name).toString();
          
          axios.get(`https://min-api.cryptocompare.com/data/pricemulti?fsyms=${names}&tsyms=USD&api_key=2947dfae54cf694ae23de9d73209be18c4b10e6b2dad970e66ba9e8d0455fc16`).then(res => {
            this.CryptosList = res.data
            console.log(this.CryptosList)
          }).catch(error => {
            console.log(error)
          })

        })
      },
      handleSubmitForm() {
        axios.post(this.baseApiURL+'/add-crypto', this.crypto).then(() => {
        console.log(this.crypto)
        this.crypto = {
        name: ''
        }
        this.getCryptos()
        }).catch(error => {
          console.log(error)
        })
      },
      deleteCrypto(name) {
        
        let foundObj = this.Cryptos.find(x => x.name === name)

        let indexOfArrayItem = this.Cryptos.findIndex(i => i._id === foundObj._id);

        axios.delete(`${this.baseApiURL}/delete-crypto/${foundObj._id}`).then(() => {
          this.Cryptos.splice(indexOfArrayItem,1);

          this.getCryptos()
        }).catch(error => {
          console.log(error)
        });
      },
      formatPrice(value) {
        return value.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1,');
      },
    }
  }

</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,300;0,500;0,600;0,700;1,400&display=swap');
  * {
    box-sizing: border-box;
  }
  body {
    background: #383838;
    color: white;
    margin: 0;
    height: 100vh;
    font-family: 'Montserrat';
  }

  .logo {
    width: 12em;
    margin: 2.5em 0;
  }

  .container{
    text-align: center; 
    margin: 0 1em;
  }

  .form-group{
    text-align: left;
    width: 300px;
    margin: 7em auto;
  }

  label{
    font-weight: bold;
    margin-bottom: 0.7em;
    display: block;
  }
  
  ::placeholder {
      color: rgba(111, 8, 178, 0.46);}

  input{
    color: #6F08B2;
    /*text-transform: uppercase;*/
    padding: 1em;
    outline: none;
    border: none;
    border-top-left-radius: 0.3em;
    border-bottom-left-radius: 0.3em;
    font-size: 1.3em;
  }

  .form-group-inner{
    display: grid;
    grid-template-columns: 100px auto;
  }

  button.add{
    font-family: 'Montserrat';
    font-size: 1em;
    font-weight: bold;
    color: white;
    background: #6F08B2;
    /*background: linear-gradient(45deg, #6F08B2, 75%, #DE45C4);*/
    border: none;
    outline: none;
    padding: 1em 3em;
    border-top-right-radius: 0.3em;
    border-bottom-right-radius: 0.3em;

  }

  p.info{
    color: #AAAAAA;
  }

  .crypto-container{
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(340px, 1fr));
    grid-gap: 1em;
  }

  .crypts{
    /*background: linear-gradient(45deg, #6F08B2, 75%, #6CCFF6);*/
    background: linear-gradient(45deg, #6F08B2, 75%, #DE45C4);
    border-radius: 0.3em;
  }

  .price{
    font-size: 2.5em;
    font-weight: 200;
    padding: 0 1em;
  }

  .lower{
    background: #57068C;
    padding: 1em;
    position: relative;
    height: 60px;
  }

  .crypto-label{
    text-transform: uppercase;
    font-size: 1.5em;
    font-weight: bold;
    margin-top: -1.25em;
    color: #E5E5E5;
  }

  .delete-btn{
    background: none;
    border: none;
    position: absolute;
    right: 1em;
    bottom: 1em;
    top: 1em;
    cursor: pointer;
    outline: none;
  }
</style>
