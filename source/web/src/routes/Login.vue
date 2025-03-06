<template>
  <div>
    <div class="container h-100">
      <div class="row h-100 justify-content-center align-items-center">
        <div class="col-md-6 text-center">
          <h2>Please Sign In</h2>
          <button v-if="federatedSignIn" class="btn btn-primary" @click="signIn">
            Sign In with Cognito
          </button>
          <amplify-authenticator v-else
              :authConfig="{ signInConfig: { isSignUpDisplayed: false } }"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Auth } from "aws-amplify";
import { AmplifyEventBus } from "aws-amplify-vue";
export default {
  name: "Login",
  data() {
    return {};
  },
  mounted() {
    AmplifyEventBus.$on("authState", (eventInfo) => {
      if (eventInfo === "signedIn") {
        this.$router.push({ name: "home" });
      } else if (eventInfo === "signedOut") {
        this.$router.push({ name: "login" });
      }
    });
  },
  created() {
    this.getLoginStatus();
  },
  methods: {
    async signIn() {
      await Auth.federatedSignIn();
    },
    async signOut() {
      await Auth.signOut();
    },
    async getLoginStatus() {
      try {
        const user = await Auth.currentAuthenticatedUser();
        if (user) {
          this.$router.push({ name: "home" });
        }
      } catch (err) {
        console.log("User must log in");
      }
    },
  },
};
</script>

<style scoped></style>
