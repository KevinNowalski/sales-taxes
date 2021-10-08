<template>
  <div class="home">
     <form action="">
      <input type="text" placeholder="Enter product...">
      <button @click="submit()">Submit</button>
    </form>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'Home',
   methods: {
    submit(){
      this.$router.push({path: '/about'})
    }
  }
});
</script>
