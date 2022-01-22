<template>
  <ul>
    <li><input v-model="username" placeholder="Username" /></li>
    <li><input v-model="token" type="password" placeholder="Token"/></li>
    <li><button v-on:click="load">Load data</button></li>
  </ul>
  <ul>
    <li v-for="item in work" :key="item.id">
      ({{ item.state }})
      <a :href="item.url">{{ item.title }}</a>
      ({{ item.author }})
    </li>
  </ul>
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
ul {
  list-style-type: none;
  padding: 0;
}
</style>
