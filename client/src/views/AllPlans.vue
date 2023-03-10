<template>
  <div class="AllPlans">
    <!--title-->
    <div class="title">
      <h1>My weekly plans</h1>
    </div>
    <!--alert if no recipes-->
    <div class="alert-holder" v-show="plans.length === 0">
      <svg xmlns="http://www.w3.org/2000/svg" style="display: none;">
        <symbol id="exclamation-triangle-fill" fill="currentColor" viewBox="0 0 16 16">
          <path d="M8.982 1.566a1.13 1.13 0 0 0-1.96 0L.165 13.233c-.457.778.091 1.767.98 1.767h13.713c.889 0 1.438-.99.98-1.767L8.982 1.566zM8 5c.535 0 .954.462.9.995l-.35 3.507a.552.552 0 0 1-1.1 0L7.1 5.995A.905.905 0 0 1 8 5zm.002 6a1 1 0 1 1 0 2 1 1 0 0 1 0-2z"/>
        </symbol>
      </svg>
        <div class="alert alert-warning d-flex align-items-center" role="alert">
          <svg class="bi flex-shrink-0 me-2" width="24" height="24" role="img" aria-label="Warning:"><use xlink:href="#exclamation-triangle-fill"/></svg>
            You have no Weekly plans yet.
        </div>
      </div>
    <!--buttons 'Add plan' and 'Delete all' + form to add plan-->
    <div class="content">
      <div class="buttons-and-form">
        <div class="buttons">
          <button class="add-btn btn-sm" v-on:click="addPlan = !addPlan">Add new plan</button>
          <button class="delete-btn btn-sm" v-on:click="deleteAllPlans">Delete all plans</button>
        </div>
        <div class="plan-form" v-if="addPlan">
          <label for="week-number" v-if="addPlan">Week number:</label>
          <b-form-input id="week" v-model="week" v-if="addPlan"/>
          <label for="year" v-if="addPlan">Year:</label>
          <b-form-input id="year" v-model="year" v-if="addPlan" />
          <button class="save-plan" v-on:click="savePlan" v-if="addPlan">Save</button>
          <button class="cancel-form" v-if="addPlan" v-on:click="cancelSavePlan">Cancel</button>
          <div class="alert alert-warning d-flex align-items-center" role="alert" v-if="weekOutsideOfRange">
            <svg class="bi flex-shrink-0 me-2" width="24" height="24" role="img" aria-label="Warning:"><use xlink:href="#exclamation-triangle-fill"/></svg>
            The week number should be greater that 0 and less than 52.
          </div>
          <!--alert if year is less that current and greater that next year-->
          <div class="alert alert-warning d-flex align-items-center" role="alert" v-if="yearLessThanCurrent">
            <svg class="bi flex-shrink-0 me-2" width="24" height="24" role="img" aria-label="Warning:"><use xlink:href="#exclamation-triangle-fill"/></svg>
            Select between current or next year.
          </div>
          <!--alert if weekly calendar already exists-->
          <div class="alert alert-warning d-flex align-items-center" role="alert" v-if="weeklyCalendarAlreadyExists">
            <svg class="bi flex-shrink-0 me-2" width="24" height="24" role="img" aria-label="Warning:"><use xlink:href="#exclamation-triangle-fill"/></svg>
            Weekly plan for selected week already exists.
          </div>
        </div>
      </div>
      <!--display all existing plans-->
      <div class="all-existing-plans">
        <b-row v-show="plans.length > 0">
          <b-col cols="12" lg="6" sm="12" v-for="(plan, index) in plans" v-bind:key="index">
            <div id="single-plan-display">
              <h5 v-on:click="goToWeeklyPlan(index)">Week {{plan.week}}  Year {{plan.year}}</h5>
              <button type="button" class="btn-close btn-close-white" aria-label="Close" @click="deleteThisPlan(index)"></button>
            </div>
          </b-col>
        </b-row>
      </div>
    </div>
  </div>
</template>

<script>
import { Api } from '@/Api'

export default {
  name: 'allPlans',
  props: ['user'],
  data() {
    return {
      plans: [],
      addPlan: false,
      week: '',
      year: '',
      weekOutsideOfRange: false,
      yearLessThanCurrent: false,
      weeklyCalendarAlreadyExists: false
    }
  },
  mounted() {
    Api.get('/profiles/' + localStorage.id + '/days?weekcalenders=true', {
      headers: {
        Authorization: 'Bearer ' + localStorage.token
      }
    })
      .then(response => {
        this.plans = response.data.weeklyCalenders
      })
      .catch(error => {
        console.error(error)
      })
  },
  methods: {
    goToWeeklyPlan(index) {
      localStorage.week = this.plans[index].week
      localStorage.year = this.plans[index].year
      this.$router.push(`/weeklyCalendar/${this.plans[index].year}/${this.plans[index].week}`)

      this.$router.go()
    },
    savePlan() {
      const currentYear = new Date().getFullYear()
      console.log(currentYear + 1)

      if (this.week < 1 || this.week > 52) {
        this.weekOutsideOfRange = true
        return
      }
      if (this.year < currentYear || this.year > (currentYear + 1)) {
        this.yearLessThanCurrent = true
      } else {
        Api.post('/profiles/' + this.user.id + '/days', {
          year: this.year,
          week: this.week,
          name: 'Monday'
        },
        {
          headers: {
            Authorization: 'Bearer ' + localStorage.token
          }
        })
          .then(response => {
            this.addPlan = false
            this.$router.go()
          })
          .catch(error => {
            console.error(error)
            if (error.response.status === 409) {
              this.weeklyCalendarAlreadyExists = true
            }
          })
      }
    },
    cancelSavePlan() {
      this.addPlan = false
      this.$router.go()
    },
    deleteAllPlans() {
      Api.delete('/profiles/' + this.user.id + '/days', {
        headers: {
          Authorization: 'Bearer ' + localStorage.token
        }
      })
        .then(response => {
          console.log(response)
        })
        .catch(error => {
          console.error(error)
        })
      this.$router.go()
    },
    deleteThisPlan(index) {
      Api.delete('/profiles/' + localStorage.id + '/days?week=' + this.plans[index].week + '&year=' + this.plans[index].year, {
        headers: {
          Authorization: 'Bearer ' + localStorage.token
        }
      })
        .then(response => {
          console.log(response)
        })
        .catch(error => {
          console.error(error)
        })
      this.$router.go()
    }
  }
}
</script>

<style lang="scss" scoped>
  .AllPlans {
    background-image: url("../assets/all-plans-background.png");
    background-size: cover;
    background-attachment: fixed;
    position: relative;
    min-height: 60vh;
  }

  .title {
    display: flex;
    width: 100%;
    padding: 3%;
    padding-bottom: 1%;
  }

  h1 {
    color: #fff;
    text-transform: uppercase;
    font-size: 50px;
    font-weight: bold;
    text-align: left;
    margin-top: 13%;
    margin-bottom: 1%;
    width: 100%;
  }

  .alert-holder .alert {
    margin-left: 3%;
    margin-right: 65%;
  }

  .content {
    display: flex;
    flex-direction: row;
    min-height: 70vh;
  }

  .buttons-and-form {
    display: flex;
    flex-direction: column;
    width: 60%;
    padding-left: 2%;
    padding-right: 2%;
    align-items: center;
  }

  .buttons-and-form .buttons {
    display: flex;
    flex-direction: row;
    margin-bottom: 3%;
  }

  .add-btn {
    margin-top: 1em;
    margin-bottom: 0.5em;
    margin-right: 0.5em;
    margin-left: 0.5em;
    float: center;
    align-self: auto;
    color: black;
    font-size: 16px;
    padding: 12px 25px;
    border-radius: 50px;
    display: inline-block;
    width: fit-content;
    border: 0;
    outline: 0;
    box-shadow: 0px 4px 10px 0px #d0fba08e;
    background-image: linear-gradient(135deg, #aef9744f 10%, #70a2047f 100%);
    &:hover {
    color: #fff;
    font-weight: bold;
    text-decoration: underline;
    box-shadow: 0px 4px 10px 0px #ebfbd985;
    background-image: linear-gradient(135deg, #9fad03e9 10%, #304903de 100%);
    }
  }

  .delete-btn {
    margin-top: 1em;
    margin-bottom: 0.5em;
    margin-right: 0.5em;
    margin-left: 0.5em;
    float: center;
    align-self: auto;
    color: black;
    font-size: 16px;
    padding: 12px 25px;
    border-radius: 50px;
    display: inline-block;
    width: fit-content;
    border: 0;
    outline: 0;
    box-shadow: 0px 4px 10px 0px #f9a3548e;
    background-image: linear-gradient(135deg, #f6b974ca 10%, #ff8000c7 100%);
    &:hover {
      color: #fff;
      font-weight: bold;
      text-decoration: underline;
      box-shadow: 0px 4px 10px 0px #fec7938e;
      background-image: linear-gradient(135deg, #e87c02f3 10%, #442301 100%);
    }
  }

  .plan-form {
    background-color: rgba(255, 255, 255, 0.468);
    margin-left: 5%;
    margin-right: 5%;
    height: fit-content;
    width: 100%;
    padding: 25px 25px;
    border-radius: 8px;
    border: 0;
    outline: 0;
    box-shadow: 0px 4px 10px 0px #bcff0375;
    color: rgb(71, 41, 2);
    font-size: 16px;
    font-weight: bold;
  }

  .save-plan {
    margin-top: 1em;
    margin-bottom: 0.7em;
    margin-right: 0;
    margin-left: 0.3em;
    float: center;
    align-self: auto;
    color: black;
    font-size: 16px;
    padding: 12px 25px;
    border-radius: 50px;
    display: inline-block;
    border: 0;
    outline: 0;
    box-shadow: 0px 4px 10px 0px #85db0d8e;
    background-image: linear-gradient(135deg, #eff886a3 10%, #a6ff0080 100%);
    &:hover {
      color: #fff;
      font-weight: bold;
      text-decoration: underline;
      box-shadow: 0px 4px 10px 0px #4324018e;
      background-image: linear-gradient(135deg, #9fad03e9 10%, #304903de 100%);
    }
  }

  .cancel-form {
    margin-top: 1em;
    margin-bottom: 0.7em;
    margin-right: 0;
    margin-left: 1em;
    float: center;
    align-self: auto;
    color: black;
    font-size: 16px;
    padding: 12px 25px;
    border-radius: 50px;
    display: inline-block;
    border: 0;
    outline: 0;
    box-shadow: 0px 4px 10px 0px #db7e0d8e;
    background-image: linear-gradient(135deg, #f8a486a3 10%, #ff330080 100%);
    &:hover {
      color: #fff;
      font-weight: bold;
      text-decoration: underline;
      box-shadow: 0px 4px 10px 0px #f89f9f8e;
      background-image: linear-gradient(135deg, #ad1a03d2 10%, #470000 100%);
    }
  }
  .all-existing-plans {
    width: 100%;
    padding-left: 1%;
  }

  #single-plan-display {
    margin-top: 0.7em;
    margin-bottom: 0.7em;
    margin-right: 0;
    margin-left: 0;
    padding-right: 5%;
    width: 90%;
    border-radius: 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    border: 0;
    outline: 0;
    box-shadow: 0px 4px 10px 0px #f7eca57c;
    background-image: linear-gradient(135deg, #fcf404c3 10%, #6a5502ef 100%);
  }

  h5 {
    margin-top: 0.3em;
    margin-bottom: 0.3em;
    float: center;
    font-weight: bold;
    padding-top: 10px;
    padding-bottom: 10px;
    padding-left: 10px;
    padding-right: 100px;
    color: #fff;
    font-size: 17px;
    font-weight: bold;
    &:hover {
      color: rgb(41, 16, 0);
      text-decoration: underline;
    }
  }

  @media(max-width: 768px) {
  .content {
    display: flex;
    flex-direction: column;
    min-height: 80vh;
  }
  .title {
    padding-bottom: 3%;
    align-items: center;
    justify-content: center;
  }

  h1 {
    font-size: 35px;
    margin-top: 40%;
    padding-left: 17%;
    padding-right: 10%;
  }
  .alert-holder .alert {
    margin-left: 17%;
    margin-right: 17%;
  }
  .buttons-and-form {
    width: 100%;
  }

  .buttons-and-form .buttons {
    flex-direction: row;
    width: 100%;
    margin-left: 20%;
  }

  #single-plan-display {
    margin-right: 10%;
    margin-left: 5%;
    justify-content: left;
    width: 50%;
  }

  .btn-close {
    margin-left: 15%;
    margin-right: 5%;
  }

  .add-btn {
    margin-top: 1em;
    margin-bottom: 0.5em;
    margin-right: 0;
    margin-left: 8%;
  }

  .delete-btn {
    margin-top: 1em;
    margin-bottom: 0.5em;
    margin-right: 0;
    margin-left: 3%;
  }

  .plan-form{
    margin-top: 3%;
    margin-left: 10%;
    margin-right: 10%;
    height: 40%;
    width: 80%;
  }

  .all-existing-plans {
    display: inline-block;
    align-items: center;
    justify-content: center;
    padding-left: 1%;
  }

  #single-plan-display {
    margin-left: 11%;
    width: 77%;
  }

  h5 {
    margin-left: 20%;
    padding: 10px 30px;
  }
  }
  </style>
