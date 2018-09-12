<template>
  <div>
    <h1>Git Pull Requests</h1>
    <v-form>
    <v-container>
      <v-layout row wrap>
        <v-flex xs12>
          <h3>Examples:</h3>
          <v-btn @click="gitUrl = 'https://github.com/Nozbe/WatermelonDB'">WatermelonDB</v-btn>
          <v-btn @click="gitUrl = 'https://github.com/you-dont-need/You-Dont-Need-Momentjs'">You-Dont-Need-Momentjs</v-btn>
          <v-btn @click="gitUrl = 'https://github.com/byoungd/English-level-up-tips-for-Chinese'">English-level-up-tips-for-Chinese</v-btn>
          <v-btn @click="gitUrl = 'https://github.com/TheAlgorithms/Python'">Python</v-btn>
        </v-flex>
        <v-flex xs12>
          <v-text-field
            label="Git URL"
            v-model="gitUrl"
          ></v-text-field>
        </v-flex>
        <v-flex xs12>
          <div v-if="apiError" class="red--text">
            <h4>{{ apiError }}</h4>
          </div>
          <div v-else-if="loading">
            <v-progress-circular
              :size="50"
              color="primary"
              indeterminate
            ></v-progress-circular>
          </div>
          <div v-else-if="!pullReqs && gitUrl">
            <h4>No Pull Requests</h4>
          </div>
          <div v-else-if="pullReqs">
            <h3>{{ pullCount }} Pull Request{{pullCount != 1 ? 's': ''}}</h3>
            <v-layout row wrap>
              <PullRequest
                v-for="req in pullReqs"
                :key="req.id"
                :request="req" />
            </v-layout>
          </div>
        </v-flex>
      </v-layout>
    </v-container>
  </v-form>
  </div>
</template>

<script>
import axios from 'axios'
import PullRequest from './PullRequest'

export default {
  name: 'Home',
  components: {
    PullRequest
  },
  data () {
    return {
      gitUrl: '',
      apiError: false,
      loading: false,
      response: ''
    }
  },
  computed: {
    pullReqs: {
      get: function () {
        if (this.response)
          return this.response.data
      },
      set: function (newValue) {}
    },
    pullCount: function () {
      if (this.response)
        return this.response.data.length
      else {
        return 0
      }
    }
  },
  watch: {
    gitUrl: function (url) {
      let giturl = new URL(url)
      let user = giturl.pathname.split('/')[1]
      let repo = giturl.pathname.split('/')[2]
      let prUrl = `https://api.github.com/repos/${user}/${repo}/pulls`
      this.loading = true
      this.apiError = false
      this.pullReqs = null
      axios.get(prUrl).then(
        response => {
          this.loading = false
          this.response = response
        }
      ).catch(
        e => {
          this.loading = false
          this.apiError = `${e.response.status} ${e.response.statusText} - ${e.response.data.message}`
        }
      )
    }
  }
}
</script>

<style scoped>
</style>
