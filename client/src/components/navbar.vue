<template>
  <div id="mnpbar">
    <nav class="navbar navbar-expand-md mnpbar">
      <div class="container-fluid">
        <a class="navbar-brand" href="/userHome">
          <img id="logo"  src="@/assets/logo.png" alt="" width="30" height="24" class="d-inline-block align-text-top" > MNP
        </a>
        <button class="navbar-toggler collapsed d-flex d-md-none flex-column justify-content-end" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
          <span class="toggler-icon top-bar"></span>
          <span class="toggler-icon middle-bar"></span>
          <span class="toggler-icon bottom-bar"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
          <div class="navbar-links ml-auto mb-3 mb-lg-0" v-if="!user">
            <ul class="navbar-links">
              <li class="nav-item"><b-link class="link" :to="{ name: 'home' }">Login</b-link></li>
              <li class="nav-item"><b-link class="link" :to="{ name: 'register' }">Register</b-link></li>
            </ul>
          </div>
          <div class="navbar-links ml-auto mb-3 mb-lg-0" v-if="user">
              <ul class="navbar-links">
              <li><b-link class="link" :to="{ name: 'addRecipe' }">AddRecipe</b-link></li>
              <li><b-link class="link" :to="{ name: 'allPlans' }">WeeklyPlans</b-link></li>
              <li><b-link class="link" :to="{ name: 'shoppingList' }">ShoppingLists</b-link></li>
              <li><b-link class="link" :to="{ name: 'profile' }">Profile</b-link></li>
              <li><a href="/" @click="handleLogOut()" class="link">Logout</a></li>

            </ul>
          </div>
        </div>
      </div>
    </nav>
  </div>
</template>

<script>
export default {
  name: 'Navbar',
  props: ['user'],
  methods: {

    handleLogOut() {
      localStorage.removeItem('token')
      localStorage.removeItem('id')
      return this.$router.go(0)
    }
  }
}
</script>

<style lang="scss" scoped>
  .mnpbar {
    background-color: rgba(9, 2, 2, 0.9);
    z-index: 99;
    width: 100%;
    position: fixed;
    transition: .5 ease all;
    color:#fff;
  }

  .navbar-brand {
    color: rgb(12, 156, 31);
    font-weight: bold;
    font-size: 20px;
  }
  #logo{
    margin-right: 10%;
  }

  .navbar-toggler {
    width: 20px;
    height: 20px;
    position: relative;
    background: transparent;
    transition: .5s ease-in-out;
 }
  .navbar-toggler,
  .navbar-toggler:focus,
  .navbar-toggler:active,
  .navbar-toggler-icon:focus {
    outline: none;
    box-shadow: none;
    border: 0;
    position: relative;
  }

  .navbar-toggler span {
    margin: 0;
    padding: 0;
  }

  .toggler-icon {
    display: block;
    position: absolute;
    height: 3px;
    width: 100%;
    background: #f6f5f5;
    border-radius: 1px;
    opacity: 1;
    left: 0;
    transform: rotate(0deg);
    transition: .25s ease-in-out;
  }

  .middle-bar {
    margin-top: 0px;
  }

  /* State when the navbar is collapsed */

  .navbar-toggler.collapsed .top-bar {
    position: absolute;
    top: 0px;
    transform: rotate(0deg);
  }

  .navbar-toggler.collapsed .middle-bar {
    opacity: 1;
    position: absolute;
    top: 10px;
    filter: none;
  }

  .navbar-toggler.collapsed .bottom-bar {
    position: absolute;
    top: 20px;
    transform: rotate(0deg);
  }

  /* when navigation is clicked */

  .navbar-toggler .top-bar {
    top: inherit;
    transform: rotate(135deg);
  }

  .navbar-toggler .middle-bar {
    opacity: 0;
    top: inherit;
    filter: none;
  }

  .navbar-toggler .bottom-bar {
    top: inherit;
    transform: rotate(-135deg);
  }

  /* Color of 3 lines */

  .navbar-toggler.collapsed .toggler-icon {
    background: linear-gradient( 263deg, rgba(15, 200, 33, 0.962) 0%, rgba(18, 80, 3, 0.945) 100% );
  }

.navbar-links {
    height: 100%;
    font-weight: 500;
    color:rgb(12, 156, 31);
    list-style: none;
    text-decoration: none;
}

.navbar-links ul {
  display: flex;
  margin: 0;
  padding: 0;
  transition: .5 ease all;
}

.navbar-links li {
  display: block;
  list-style: none;
  flex-direction: row;
  margin-right: 50px;
   &:hover {
  color:rgba(29, 137, 209, 0.971);
  border-color:rgba(15, 63, 95, 0.971);
  }
}

.navbar-links li a {
  display: block;
  margin-right: 5%;
  text-decoration: none;
  color:rgb(12, 156, 31);
  border-bottom: 1px solid transparent; // to add a hover effect

  &:hover {
  color:rgba(29, 137, 209, 0.971);
  border-color:rgba(15, 63, 95, 0.971);
  }
}

@media screen and (max-width: 768px) {
  .navbar-links ul {
    width: 100%;
    flex-direction: column;
    align-items: center;
  }

  .navbar-links ul li {
    text-align: center;
  }

  .navbar-links ul li a {
    padding: .5rem 1rem;
  }

  .navbar-links.active {
    display: flex;
  }
}
</style>
