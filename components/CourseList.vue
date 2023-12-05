<template>
  <div>
    <div v-for="course in courses" :key="course.$id">
      <h2>{{ course.name }}</h2>
      <p>{{ course.description }}</p>
      <p>{{ course.tutor }}</p>
      <p>Price: ${{ course.price }}</p>
      <button @click="buyCourse()">Buy Now</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    courses: Array,
    buyCourse: Function
  },
};
</script>
  