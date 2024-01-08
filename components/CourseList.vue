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
  data() {
    return {
      courses: [
        {
          name: "Introduction to JavaScript",
          description: "Embark on a comprehensive journey to grasp the fundamental concepts of the JavaScript programming language. This engaging learning experience is tailored for beginners, offering a step-by-step exploration of key JavaScript principles, syntax, and functionalities. Discover the power of JavaScript as a versatile scripting language widely employed for both front-end and back-end web development.",
          tutor: "John Doe",
          price: 10
        },
      ],
    };
  },
  props: {

    buyCourse: Function
  },
};
</script>
  