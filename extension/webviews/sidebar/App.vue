<template>
  <div v-if="loaded">
    <h2 v-if="user">
      Hello {{ getUserFirstName }}
      <button @click="logout">Logout</button>
    </h2>
    <div v-else>
      <button @click="sign_in">Sign In with github</button>
    </div>
  </div>
  <div v-else>Loading...</div>
</template>
<script>
import Vue from "vue";
import { mapGetters, mapState } from "vuex";
export default Vue.extend({
  data() {
    return {
      task: "",
      tasks: [],
      completed: [],
      loaded: false,
    };
  },
  async mounted() {

    window.addEventListener("message", async (event) => {
      const message = event.data;
      switch (message.type) {
        case "token": {
          const token = message.value;
          this.$store.commit("SET_ACCESS_TOKEN", token);
          this.loaded = false;
          console.log("before", this.loaded);
          await this.$store.dispatch("fetchUserInfo");
          this.loaded = true;
          console.log("after", this.loaded);
          break;
        }
      }
    });

    _vscode.postMessage({ type: "git-token" });
  },
  computed: {
    ...mapState(["user"]),
    ...mapGetters(["getUserFirstName"]),
  },
  methods: {
    addTask() {
      this.tasks.push(this.task);
      console.log("qwe:", { hello: _vscode });
      _vscode.postMessage({
        type: "onInfo",
        value: `${this.task} added to the tasks :)`,
      });
      this.task = "";
    },
    markAsDone(task) {
      let idx = this.tasks.indexOf(task);
      this.tasks.splice(idx, 1);
      console.log({ task });
      this.completed.push(task);

      this.$vscode.postMessage({
        type: "onInfo",
        value: `${task} marked as completed :)`,
      });
    },
    sign_in() {
      _vscode.postMessage({ type: "authenticate" });
    },
    logout() {
      _vscode.postMessage({ type: "logout" });
      this.$store.dispatch("logout");
    },
  },
});
</script>
<style scoped>
h1 {
  color: pink;
}
</style>