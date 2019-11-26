<template>
  <div class="header">
    <b-row>
      <b-col md="4" offset-md="4" class="text-center">
        <img center src="@/assets/dragon-logo.png" height="70" class="navbar-logo" alt="Kitten" />
      </b-col>
    </b-row>
    <b-row>
      <b-col md="4" offset-md="4" class="app-title text-center">{{appname}}</b-col>
    </b-row>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      appname: "MediaCorp's Dragon Fight"
    };
  }
};
</script>

<style scoped>
.header {
  height: 100px;
}
.app-title {
  font-size: 20px;
  font-weight: 600;
  color: #ff9900;
}
</style>