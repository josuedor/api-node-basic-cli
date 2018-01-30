<template>
  <div id="app">

    <div class="container">
      <div class="row">
        <div class="col-xs-12 col-md-12">
          <b-img center src="src/assets/twitter.png" alt="center image" />
          <center><span class="text-muted"><a href="https://github.com/josuedor">@josuedor</a></span></center>
        </div>
        <div class="col-xs-12 col-md-4">
          <div class="col-xs-12 col-md-12 color-aqua">
          <b-form-group id="groupcontent"
                          label="Content"
                          label-for="content"
                          description="Write the content of tweet">
              <b-form-textarea id="content"
                            type="text"
                            v-model="tweet.content"
                            :rows="3"
                            :max-rows="6"
                            required
                            placeholder="Enter content of tweet">
              </b-form-textarea>
          </b-form-group>

          <b-form-group id="grouplocation"
                          label="Location"
                          label-for="location"
                          description="Write the location">
              <b-form-input id="location"
                            type="text"
                            v-model="tweet.location"
                            required
                            placeholder="Enter your location">
              </b-form-input>
          </b-form-group>
          
          <b-form-group id="groupuser"
                          label="User:"
                          label-for="user">
              <b-form-select id="user"
                            required
                            v-model="tweet.user" class="select">
                            <option v-for="(user, key) in users" :key="key" :value="user._id">
                              {{key}} - {{ user.firstname }} {{ user.lastname }}
                            </option>
              </b-form-select>
          </b-form-group>

          <b-button variant="success" @click.prevent="create">Create</b-button>
          <hr />
          </div>
        </div>
          
          
          
          <div class="col-xs-12 col-md-8"> 
          <hr />
          <p v-if="loading">Loading ...</p>
          
          <b-list-group v-else>
            <b-list-group-item href="#" class="flex-column align-items-start" v-for="tweet in tweets" :key="tweet._id">
              <div class="d-flex w-100 justify-content-between">
                <h5 class="mb-1">{{ tweet.user.firstname }} {{ tweet.user.lastname }}</h5>
                <small>{{moment(tweet.createdAt, "YYYYMMDD").fromNow()}}</small>
              </div>
              <p class="mb-1">
                {{ tweet.content }}
              </p>
              <small>{{ tweet.location }}</small>
            </b-list-group-item>
          </b-list-group>

        </div>
        
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
import config from "./config.json";

export default {
  name: 'app',
  data () {
    return {
      loading: true,
      tweets: [],
      users: [],
      tweet: {
        content: '',
        location: '',
        user: ''
      }
    }
  },
  created (){
    this._loadTweets();
    this._loadUsers();
  },
  methods: {
    _loadTweets(){
      axios.get(`${config.baseURL}/tweets?limit=20`,{
        withCredentials: true
      })
      .then( response => {
        this.loading = false;
        this.tweets = response.data.data;
      })
    },
    _loadUsers(){
      axios.get(`${config.baseURL}/users`,{
        withCredentials: true
      })
      .then( response => {
        this.users = response.data.data;
      })
    },
     moment: function (date) {
      return moment(date);
    },
    create(){
      axios.post(`${config.baseURL}/tweets`, this.tweet, {
        withCredentials: true,
        headers: {
          'Content-Type': 'application/json'
        }
      })
      .then( response => {
        this._loadTweets();
      })
    }
  }
}
</script>

<style>
  .color-aqua{
    background-color: #f0f8ff;
  }

  .footer {
    bottom: 0;
    width: 100%;
    /* Set the fixed height of the footer here */
    height: 60px;
    line-height: 60px; /* Vertically center the text there */
    background-color: #f5f5f5;
  }
</style>
