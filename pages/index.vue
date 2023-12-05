<template>
  <div>
    <div v-if="authUser === false" class="section">
      <p>
        Only registered users can create subscriptions.
      </p>

      <button @click="register()">
        <span>Create account</span>
      </button>
    </div>

    <div v-if="authUser">
      <button @click="signOut()" class="sign-out">
        <span>Sign out</span>
      </button>

      <CourseList :courses="courses" :buyCourse="buyCourse" v-if="!authUser.labels.includes('subscriber')"
        class="section" />

      <div v-if="authUser.labels.includes('subscriber')">
        <CoursePage :course="courses[0]" class="section" />
      </div>
    </div>
  </div>
</template>

<style>
body {
  background-color: aliceblue;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}

.section {
  margin: 0 auto;
  width: 400px;
  border: 2px solid rebeccapurple;
  padding: 10px;
  border-radius: 5px;
  margin-top: 100px;
}

button {
  background-color: black;
  border: 1px solid white;
  color: white;
  border-radius: 5px;
  width: 150px;
  height: 35px;
  font-size: medium;
}

button:hover {
  cursor: pointer;
  background-color: rgb(51, 51, 51);
}

.sign-out {
  background-color: white;
  color: red;
  border: 1px solid red;
}
</style>

<script>
import { Account, Client, Functions } from 'appwrite';

const client = new Client()
  .setEndpoint('https://cloud.appwrite.io/v1')
  .setProject('[PROJECT-ID]');

const account = new Account(client);

const functions = new Functions(client)

export default {
  name: 'IndexPage',
  data() {
    return {
      authUser: null,
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
  async mounted() {
    await this.loadAuthUser();
  },
  watch: {
    authUser: {
      immediate: true,
      handler: 'loadAuthUser',
    },
  },
  methods: {
    async loadAuthUser() {
      try {
        this.authUser = await account.get();
      } catch (err) {
        console.warn(err, this.authUser);
        this.authUser = false;
      }
    },
    async register() {
      await account.createAnonymousSession();
      await this.loadAuthUser();
    },
    async signOut() {
      await account.deleteSession('current');
      this.authUser = false;
    },
    async buyCourse() {
      try {
        const execution = await functions.createExecution(
          '[FUNCTION-ID]',
          JSON.stringify({
            failureUrl: window.location.href,
            successUrl: window.location.href,
          }),
          false,
          '/subscribe',
          'POST',
          {
            'Content-Type': 'application/json',
          }
        );
        const url =
          execution.responseHeaders.find(
            (header) => header.name === 'location'
          ) ?? {};

        window.location.replace(url.value ?? '/');
      } catch (error) {
        console.error('Error fetching courses', error);
      }
    },
  }
}
</script>
