<template>
  <div
    class="container-fluid p-3"
    style="background-color: black; min-height: 100vh"
  >
    <div class="mb-3">
      <h3 class="text-success mb-2">Latest Commits</h3>
      <div class="d-flex align-items-center">
        <button @click="toggleMessages" class="btn btn-success mr-2">
          {{ showMessages ? "Hide" : "Show" }} Commit Messages
        </button>
        <input
          v-model.number="inputCommitsPerPage"
          @keyup.enter="updateCommitsPerPage"
          type="number"
          class="form-control d-inline-block w-auto mr-2"
          placeholder="Commits per page"
        />
        <button @click="updateCommitsPerPage" class="btn btn-success">
          Refresh
        </button>
      </div>
    </div>
    <ul class="list-unstyled bg-white rounded p-2">
      <template
        v-for="{ html_url, sha, author, commit } in commits"
        :key="html_url"
      >
        <li class="mb-2 pb-2 border-bottom">
          <a
            :href="html_url"
            target="_blank"
            class="text-dark font-weight-bold d-inline-block mr-2"
          >
            {{ sha.substring(0, 7) }}
          </a>
          <span class="text-muted d-inline-block">by</span>
          <i class="fa fa-fw fa-user"></i>
          <span class="font-weight-bold d-inline-block mr-2">
            <a :href="author.html_url" target="_blank" class="text-success">
              {{ commit.author.name }}
            </a>
          </span>
          <span class="text-muted d-inline-block mr-1">at</span>
          <span class="font-weight-bold text-dark d-inline-block">
            {{ formatDate(commit.author.date) }}
          </span>
          <span v-if="showMessages" class="d-block mt-1 text-muted">
            {{ truncate(commit.message) }}
          </span>
        </li>
      </template>
    </ul>
  </div>
</template>

<script>
import moment from "moment";
export default {
  data: function () {
    return {
      API_URL: `https://api.github.com/repos/vuejs/core/commits?per_page=10&sha=main`,
      commits: null,
      showMessages: true,
      commitsPerPage: 10,
      inputCommitsPerPage: 10,
    };
  },
  created: function () {
    this.fetchData();
  },
  methods: {
    fetchData: async function () {
      this.API_URL = `https://api.github.com/repos/vuejs/core/commits?per_page=${this.commitsPerPage}&sha=main`;
      this.commits = await (await fetch(this.API_URL)).json();
    },
    truncate: function (text) {
      const newlineIdx = text.indexOf("\n");
      return newlineIdx > 0 ? text.slice(0, newlineIdx) : text;
    },
    formatDate: function (date) {
      return moment(date).format("ddd MMM DD YYYY HH:mm:ss [GMT]ZZ");
    },
    toggleMessages: function () {
      this.showMessages = !this.showMessages;
    },
    updateCommitsPerPage: function () {
      this.commitsPerPage = this.inputCommitsPerPage;
      this.fetchData();
    },
  },
};
</script>

<style scoped>
ul {
  border-radius: 5px;
}

li:last-child {
  border-bottom: none !important;
}
</style>
