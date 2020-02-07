<template>
  <!-- <div> -->
  <div id="app">
    <p>
      <input type="text" v-model="keyword" />
    </p>
    <p>{{ message }}</p>
    <p>
      <li v-for="item in items">
        <!-- {{ item.title }} -->
        <a v-bind:href="item.url" target="_blank">{{ item.title }}</a>
        like: {{ item.likes_count }}
      </li>
    </p>
  </div>
  <!-- </div> -->
</template>

<script>
import axios from "axios";
import lodash from "lodash";

export default {
  name: "SearchClient",
  data: function() {
    return {
      items: null,
      keyword: "",
      message: ""
    };
  },
  watch: {
    keyword: function(newKeyword, oldKeyword) {
      //console.log(newKeyword);
      this.message = "Waiting for you to stop typing...";
      this.debouncedGetAnswer();
    }
  },
  created: function() {
    this.debouncedGetAnswer = _.debounce(this.getAnswer, 1000);
  },
  methods: {
    getAnswer: function() {
      if (this.keyword === "") {
        this.items = null;
        this.message = "";
        return;
      }

      this.message = "Loading...";
      var vm = this;
      var params = { page: 1, per_page: 20, query: this.keyword };
      axios
        .get("https://qiita.com/api/v2/items", { params })
        .then(function(response) {
          console.log(response);
          vm.items = response.data;
        })
        .catch(function(error) {
          vm.message = "Error!" + error;
        })
        .finally(function() {
          vm.message = "";
        });
    }
  }
};
</script>
