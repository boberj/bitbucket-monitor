<template>
  <section class="section">
    <div class="container">
      <div class="field">
        <label class="label">Username</label>
        <div class="control has-icons-left">
          <input class="input" type="text" v-model="username" />
          <span class="icon is-small is-left">
            <i class="fas fa-user"></i>
          </span>
        </div>
      </div>
      <div class="field">
        <label class="label">Token</label>
        <div class="control has-icons-left">
          <input class="input" type="password" v-model="token" />
          <span class="icon is-small is-left">
            <i class="fas fa-lock"></i>
          </span>
        </div>
      </div>
      <div class="field">
        <div class="control">
          <button class="button is-link" v-on:click="load">Load data</button>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="content" v-for="item in work" :key="item.id">
        <span class="tag">
          {{ item.state }}
        </span>
        <a :href="item.url">{{ item.title }}</a>
        <small>({{ item.author }})</small>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      username: "",
      token: "",
      response: {},
      work: [],
    };
  },
  methods: {
    load() {
      const baseUrl = "https://bitbucket.navico.com/rest/api/1.0";
      const dashboard = "/dashboard/pull-requests";
      const url = baseUrl + dashboard;
      const init = {
        headers: new Headers({
          Authorization: "Basic " + btoa(`${this.username}:${this.token}`),
        }),
        credentials: "include",
      };

      fetch(url, init)
        .then((r) => r.json())
        .then((r) => {
          this.response = r;

          this.work = r.values.reduce((acc, item) => {
            acc.push({
              id: item.id,
              state: item.state,
              title: item.title,
              author: item.author.user.displayName,
              url: item.links.self[0].href,
            });
            return acc;
          }, []);
        });
    },
  },
};
</script>

<style scoped>
</style>
