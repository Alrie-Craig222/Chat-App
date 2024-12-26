<template>
  <v-container class="d-flex flex-column align-center justify-center main-background">
    <v-row justify="center">
      <v-col cols="12">
        <div class="gradient-card">
          <login-form>
            <template #header>
              <h2 class="text-center text-light mt-5">Welcome Back!</h2>
            </template>
            <template #footer>
              <v-btn text @click="switchToRegister" class="mt-3 text-wrap text-light"
                >Don't have an account?    <strong>Register</strong></v-btn
              >
            </template>
          </login-form>
        </div>
      </v-col>
    </v-row>

    <v-dialog v-model="showRegister" persistent max-width="400px">
      <div class="gradient-card">
        <register-form>
          <template #header>
            <h2 class="text-center text-light">Create an Account</h2>
          </template>
          <template #footer>
            <v-btn text @click="switchToLogin" class="mt-3 text-wrap text-light"
              >Already have an account?   <strong>Login</strong></v-btn
            >
          </template>
        </register-form>
      </div>
    </v-dialog>
  </v-container>
</template>

<script>
import { ref } from "vue";
import LoginForm from "@/layouts/LoginForm.vue";
import RegisterForm from "@/layouts/RegisterForm.vue";

export default {
  components: { LoginForm, RegisterForm },
  setup() {
    const showRegister = ref(false);

    const switchToLogin = () => (showRegister.value = false);
    const switchToRegister = () => (showRegister.value = true);

    return { showRegister, switchToLogin, switchToRegister };
  },
};
</script>

<style scoped>
.main-background {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #891bfd, #4588fd);
}

.gradient-card {
  padding: 20px;
  border-radius: 10px;
  background: linear-gradient(135deg, #6a11cb, #2575fc); 
  box-shadow: 0px 4px 15px rgba(0, 0, 0, 0.2);
  color: white;
}

.text-light {
  color: white;
}

.v-btn {
  color: white !important; 
}
</style>
