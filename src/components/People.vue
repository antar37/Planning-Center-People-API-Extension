<template>
<div class="container-fluid">
  <div class="people row">
    <div class="col-xl-2" v-for="person in people" v-bind:key="person.id">
      <div class="card person">
        <img class="card-img-top" v-bind:src="person.attributes.avatar" v-bind:alt='person.attributes.first_name+" "+person.attributes.last_name'>
        <div class="card-body">
          <h5 class="card-title" v-bind:class="person.attributes.gender">{{person.attributes.first_name}} <span v-if="person.attributes.nickname">"{{person.attributes.nickname}}"</span> {{person.attributes.last_name}}</h5>
          <p class="card-text">

            <span v-if="person.attributes.email" style="font-size: 18px">📧: <span style="font-size: 16px">{{person.attributes.email[0]}}</span> </span> <br/>
            <span v-if="person.attributes.households" style="font-size: 18px">🏡: <span style="font-size: 16px">Casa {{person.attributes.households.name.replace('Household','')}}</span> </span>
          </p>
          <a v-bind:href="'https://people.planningcenteronline.com/people/'+person.id" target="_blank" class="btn btn-primary">Perfil</a>
        </div>
      </div>
    </div>    
  </div>
</div>  
</template>

<script>
import axios from 'axios'
import _ from 'lodash'


export default {
  data: function () {
    return {
      people: []
    }
  },
  methods: {
    fetchPeople: function async () {
     axios({
        method: 'get',
        url: 'https://api.planningcenteronline.com/people/v2/people?order=-created_at&include=addresses,emails,households,marital_status,phone_numbers,social_profiles&per_page=50&where[membership]=miembro&where[status]=active',
        auth: {
          username: process.env.VUE_APP_API_USERNAME,
          password: process.env.VUE_APP_API_PASSWORD
        }
      }).then(({data})=>{
        console.log(data);
        var list = _.map(data.data, info =>{

            var email =  
              _.map(
                _.result(info, 'relationships.emails.data' ), obj => 
                _.result(
                  _.find(data.included, {id: obj.id}), 
                'attributes.address')
              
            )
            
            var address = _.map(_.result(info, 'relationships.addresses.data'), obj => 
                _.result(
                    _.find(data.included, {id: obj.id}), 
                'attributes')
            )[0]   
            var households = _.map(_.result(info, 'relationships.households.data'), obj => 
                _.result(
                    _.find(data.included, {id: obj.id}), 
                'attributes')
            )[0] 
            var marital_status = _.map(_.result(info, 'relationships.marital_status.data'), obj => 
                _.result(
                    _.find(data.included, {id: obj.id}), 
                'attributes')
            )[0]
            var phone_numbers = _.map(_.result(info, 'relationships.phone_numbers.data'), obj => 
                _.result(
                    _.find(data.included, {id: obj.id}), 
                'attributes')
            ) 
            var social_profiles = _.map(_.result(info, 'relationships.social_profiles.data'), obj => 
                _.result(
                    _.find(data.included, {id: obj.id}), 
                'attributes')
            ) 
            var person = {
                id: info.id,
                attributes: {
                    ...info.attributes, 
                    email, 
                    address,
                    households,
                    marital_status,
                    phone_numbers,
                    social_profiles
                }
            }
           return person
        })
        this.people = list
        return list;
      }, (error) => {
        console.log(error)
      })
    }
  },
  mounted: function () {
    this.fetchPeople()
  }
}
</script>


<style>
.people{
  display:flex;
  flex: 1;
  flex-wrap:wrap;
}
.person{
  margin-bottom:30px;
}
.person img{
  height:250px;
  object-fit:cover;
}

.person .M{
  color: navy;
}

.person .F{
  color: palevioletred;
}
</style>
