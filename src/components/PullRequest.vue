<template>
  <v-flex xs12 sm6 lg3>
    <v-card>
        <v-img
          :src="request.user.avatar_url"
          aspect-ratio="2.75"
        ></v-img>

        <v-card-title primary-title>
          <div>
            <h3 class="headline mb-0">{{request.title}}</h3>
            <div>
              Pull Request #{{ request.number }} by {{request.user.login}}<br/>
              Created: {{ (new Date(request.created_at)).toLocaleString() }}<br/>
              Comments: {{ commentCount }}<br/>
              Commits: {{ commitCount }}<br/>
            </div>
          </div>
        </v-card-title>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn :href="request.html_url" icon>
            <v-icon>pageview</v-icon>
          </v-btn>
        </v-card-actions>
      </v-card>
  </v-flex>
</template>

<script>
import axios from 'axios'

export default {
  name: 'PullRequest',
  props: ['request'],
  data () {
    return {
      commitCount: '',
      commentCount: ''
    }
  },
  mounted: function () {
    axios.get(this.request.commits_url).then(res => this.commitCount = res.data.length)
    axios.get(this.request.comments_url).then(res => this.commentCount = res.data.length)
  }
}
</script>

<style scoped>
  * {
    text-align: left;
  }
  .v-card {
    margin: 10px;
  }
</style>
