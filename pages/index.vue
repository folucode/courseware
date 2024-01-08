<template>
  <div>
    <div v-if="authUser === null" class="section">
      <p>
        Only registered users can create subscriptions.
      </p>

      <button @click="register()">
        <span>Create account</span>
      </button>
    </div>

    <div v-if="authUser !== null">
      <button @click="signOut()" class="sign-out">
        <span>Sign out</span>
      </button>

      <div>
        <CourseList v-if="courses === null" :buyCourse="buyCourse" class="section" />
        <CoursePage v-else :course="courses[0]" class="section" />
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
import { Account, Client, Databases, Functions } from 'appwrite';

const client = new Client()
  .setEndpoint('https://cloud.appwrite.io/v1')
  .setProject('[PROJECT-ID]');

const account = new Account(client);

const functions = new Functions(client)

const databases = new Databases(client)

export default {
  name: 'IndexPage',
  data() {
    return {
      authUser: null,
      courses: null,
    };
  },
  mounted() {
    this.checkAnonymousSession();
    this.loadCourses();
  },
  methods: {
    async checkAnonymousSession() {
      try {
        const anonymousSession = await account.get();

        if (anonymousSession) {
          this.authUser = anonymousSession;
        }
      } catch (error) {
        console.error('Error checking anonymous session:', error.message);
      }
    },
    async register() {
      const user = await account.createAnonymousSession();
      this.authUser = user
    },
    async signOut() {
      await account.deleteSession('current');
      this.authUser = null;
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
    async loadCourses() {
      try {
        const courses = await databases.listDocuments(
          "[DATABASE-ID]",
          "[COLLECTION-ID]",
        )

        if (courses.total > 0) {
          this.courses = courses.documents
        }
      } catch (error) {
        console.log(error);
      }

    }
  }
}
</script>
