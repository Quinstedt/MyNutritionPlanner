<template>
<div class="SLPage">
  <div class="box-form">
    <div class="container" id="shoppingListView">
      <b-row>
        <b-col>
          <h2 id="page-title">MY SHOPPING LISTS</h2>
        </b-col>
      </b-row>
        <div class="d-flex justify-content-center">
          <b-button variant="success" class="menu-button" id="addList" v-on:click="goToAddShoppingList()">
            <b-icon icon="plus-circle"></b-icon> Add List</b-button>
          <br>
          <b-button variant="danger" class="menu-button" id="deleteList" v-on:click="deleteShoppingLists()">
            <b-icon icon="trash"></b-icon>Delete All</b-button>
        </div>
      <br>
      <br>
    <div id="shopping-lists-background">
      <div v-if="this.shoppinglists.length!==0">
      <div class="container" v-for="shoppinglist in shoppinglists" :key="shoppinglist._id">
        <b-row align-h="center" align-v="baseline">
          <b-col cols="8">
            <b-button block variant="outline-success" v-b-toggle="'collapse' + shoppinglist._id">
                Week {{shoppinglist.week}}: {{shoppinglist.name}}</b-button>
          </b-col>
          <b-col cols="3">
            <b-button-group>
              <b-button variant="outline-warning" v-on:click="goToEditSL(shoppinglist._id)">
                <b-icon icon="pencil-square"></b-icon> Edit
              </b-button>
              <b-button variant="outline-danger" v-on:click="deleteSpecificSL(shoppinglist._id)">
                <b-icon icon="trash"></b-icon> Delete
              </b-button>
            </b-button-group>
          </b-col>
          <b-row align-h="start" >
            <b-col cols="11">
                <b-collapse :id="'collapse' + shoppinglist._id">
                <b-card class="shopping-list-card">
                  <b-form-checkbox v-for="item in shoppinglist.items" :key="item._id">{{item.amount}} {{item.unit}} {{item.item}}</b-form-checkbox>
                </b-card>
              </b-collapse>
            </b-col>
          </b-row>

        </b-row>
      </div>
      <br>
      </div>
      <div v-else>
        <h3 style="color: white; text-align:center;"> Add your first shopping list here! </h3>
      </div>
    </div>
    </div>
  </div>
</div>
</template>

<script>
import { Api } from '@/Api'

export default {
  name: 'shoppingList',
  data() {
    return {
      message: '',
      shoppinglists: []
    }
  },
  beforeCreate() {
    Api.get('/profiles/' + localStorage.id + '/shoppinglists', {

      headers: {
        Authorization: 'Bearer ' + localStorage.token
      }
    })
      .then(response => {
        this.shoppinglists = response.data.shoppinglists
        this.shoppinglists.forEach(sl => {
          console.log(sl.name)
        })
      })
      .catch(error => {
        console.log(error)
        if (error.response.status !== 'undefined' && error.response.status === 404) {
          console.log('404 error found')
          //    TODO add 404 page
          // this.$router.push('/addShoppingList')
        }
      })
  },
  methods: {
    goToAddShoppingList() {
      this.$router.push('/addShoppingList')
    },
    goToEditSL(id) {
      this.$router.push(`/editShoppingList/${id}`)
    },
    deleteShoppingLists() {
      Api.delete('/profiles/' + localStorage.id + '/shoppingLists', {
        headers: {
          Authorization: 'Bearer ' + localStorage.token
        }
      }).catch(error => {
        console.log(error)
      })
    },
    deleteSpecificSL(id) {
      Api.delete('/profiles/' + localStorage.id + '/shoppingLists/' + id, {
        headers: {
          Authorization: 'Bearer ' + localStorage.token
        }
      }).catch(error => {
        console.log(error)
      })
      this.$router.go(0)
    }
  }
}
</script>

<style lang="scss" scoped>
  .SLPage {
    background-image: url("../assets/shopping-lists-background.jpg");
    background-size: cover;
    background-attachment: fixed;
    position: relative;
    min-height: 93vh;
    height: 110%;
  }

  #page-title {
    color: #fff;
    font-size: 50px;
    font-weight: bold;
    margin-top: 20%;
    margin-bottom: 2%;
    width: 100%;
    text-align: center;
  }
  #page-title h2 {
    box-shadow: 0px 4px 10px 0px black;
  }

  .box-form {
      height: 100%;
      display: flex;
      width: 100%;
      box-sizing: border-box;
      align-items: center;
      justify-content: center;
    }

    .shoppingListView {
      height: 100%;
      width: 100%;
      box-sizing: border-box;
      padding: 4%;
      padding-bottom: 1%;
      overflow: hidden;

    }
    #shopping-lists-background {
      padding: 3%;
      background-color: rgba(0, 0, 0, 0.84);
    }
    .shopping-list-card {
      color: #fff;
      margin-left: 4%;
      background-color: rgba(197, 197, 197, 0.485);
      box-shadow: 0px 4px 10px 0px #848484a6;
    }

    .menu-button {
      margin: 10px;
    }

    #addList {
    margin-top: 0.2em;
    margin-bottom: 0.01em;
    margin-right: 1em;
    margin-left: 0.01em;
    float: center;
    color: rgb(1, 17, 0);
    font-size: 16px;
    padding: 10px 25px;
    border-radius: 50px;
    display: inline-block;
    border: 0;
    outline: 0;
    box-shadow: 0px 4px 20px 0px #9bfc8c84;
    background-image: linear-gradient(135deg, #bafcae 10%, #01a304cb 100%);
      &:hover {
      color: rgb(253, 253, 253);
      text-decoration: underline;
      font-weight: bold;
      background-image: linear-gradient(135deg, #04ff00 10%, #01450d 100%);
      box-shadow: 0px 4px 10px 0px #98ff79;
    }
  }

  #deleteList {
    margin-top: 0.2em;
    margin-bottom: 0.01em;
    margin-right: 1em;
    margin-left: 0.01em;
    float: center;
    color: rgb(42, 2, 2);
    font-size: 16px;
    padding: 10px 25px;
    border-radius: 50px;
    display: inline-block;
    border: 0;
    outline: 0;
    box-shadow: 0px 4px 20px 0px #ea4e4e84;
    background-image: linear-gradient(135deg, #fa9191 10%, #a31401cb 100%);
      &:hover {
      color: rgb(253, 253, 253);
      text-decoration: underline;
      font-weight: bold;
      background-image: linear-gradient(135deg, #ff0000 10%, #450901 100%);
      box-shadow: 0px 4px 10px 0px #ff7979;
    }
  }

</style>
