<template>
  <v-container fluid>
    <v-app-bar app dense color="primary" dark>
      <v-toolbar-title>Messaging App</v-toolbar-title>
      <v-spacer></v-spacer>
      
      <router-link to="/chat" class="text-decoration-none">
        <v-btn class="gradient-btn text-white" dark><v-icon left>mdi-chat</v-icon></v-btn>
      </router-link>
      
      <v-btn @click="logout" class="gradient-btn text-white" dark>
        <v-icon left>mdi-logout</v-icon>
      </v-btn>
    </v-app-bar>

    <v-row>
      <v-col cols="12">
        <v-spinner v-if="loading" color="primary" class="d-flex justify-center"/>

       
        <v-container v-else-if="!loading && users.length > 0" class="bottoms">
          <v-row justify="center" class="mb-5">
            <h1 class="welcome-header">Welcome to Messaging App!</h1>
          </v-row>
          <v-row justify="center" class="mb-5">
          </v-row>

          <v-row class="user-statistics mb-5">
            <v-col cols="12" md="4">
              <v-card class="stat-card">
                <v-card-title class="stat-title">
                  <v-icon left>mdi-account-multiple</v-icon> Total Users
                </v-card-title>
                <v-card-text class="stat-value">{{ users.length }}</v-card-text>
              </v-card>
            </v-col>
            <v-col cols="12" md="4">
              <v-card class="stat-card">
                <v-card-title class="stat-title">
                  <v-icon left>mdi-message</v-icon> Active Chats
                </v-card-title>
                <v-card-text class="stat-value">{{ activeChats }}</v-card-text>
              </v-card>
            </v-col>
            <v-col cols="12" md="4">
              <v-card class="stat-card">
                <v-card-title class="stat-title">
                  <v-icon left>mdi-group</v-icon> Groups Created
                </v-card-title>
                <v-card-text class="stat-value">{{ groups }}</v-card-text>
              </v-card>
            </v-col>
          </v-row>

         
          <v-row>
            <v-col cols="12">
              <h2 class="shortcuts-header">Shortcuts</h2>
              <v-row>
                <v-col cols="12" md="6">
                  <v-btn
                    class="shortcut-btn gradient-btn"
                    block
                    dark
                    @click="$router.push('/contacts')"
                  >
                    <v-icon left>mdi-account-box</v-icon> View Contacts
                  </v-btn>
                </v-col>
                <v-col cols="12" md="6">
                  <v-btn
                    class="shortcut-btn gradient-btn"
                    block
                    dark
                    @click="$router.push('/groups/new')"
                  >
                    <v-icon left>mdi-account-group</v-icon> Create New Group
                  </v-btn>
                </v-col>
              </v-row>
            </v-col>
          </v-row>
        </v-container>


     
        <v-alert
          v-else-if="!loading && users.length === 0"
          type="info"
          class="mt-4"
        >
          No users found.
        </v-alert>

        
        <v-alert v-if="error" type="error" class="mt-4">
          Error fetching users: {{ error }}
        </v-alert>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";
import { useAuthStore } from "@/stores/auth";

export default {
  name: "Users",
  data() {
    return {
      users: [],
      loading: true,
      error: null,
      drawer: false, 
    };
  },
  mounted() {
    this.fetchUsers();
  },
  methods: {
    async fetchUsers() {
      try {
        const response = await axios.get("http://127.0.0.1:8000/api/users/", {
          headers: {
            Authorization: `Bearer ${localStorage.getItem("accessToken")}`,
          },
        });
        this.users = response.data.data.users;
      } catch (error) {
        this.error = error.message || "Something went wrong!";
      } finally {
        this.loading = false;
      }
    },
    logout() {
      const authStore = useAuthStore();
      authStore.logout();
    },
  },
};
</script>

<style scoped>
.gradient-header {
  background: linear-gradient(135deg, #6a11cb, #2575fc);
  padding: 20px;
  border-radius: 8px;
}

.gradient-btn {
  background: linear-gradient(135deg, #6a11cb, #2575fc);
  border: none;
  border-radius: 8px;
  font-weight: bold;
}

.gradient-icon-btn {
  background: linear-gradient(135deg, #6a11cb, #2575fc);
  color: white;
  position: absolute;
  top: 10px;
  left: 10px;
  z-index: 1000;
}

.headers {
  position: relative;
  top: -35px;
  width: 110%;
  margin-left: -20px;
}

.fonts {
  font-size: xx-large;
  font-weight: 900;
}

.arimo {
  font-family: "Arimo", serif;
  font-optical-sizing: auto;
  font-weight: 692;
  font-style: italic;
}

.tops {
  margin-top: 250px;
}

.downs {
  margin-top: -150px;
}

.bottoms {
  position: relative;
  margin-top: -20px;
}

.starts {
  margin-left: 160px;
}

.custom-data-table {
  border: 1px solid #e0e0e0; 
  border-radius: 8px;
  overflow: hidden; 
  background-color: #f9f9f9; 
}

.custom-data-table th {
  background-color: #6a11cb; 
  color: white;
  font-weight: bold;
  text-align: center;
  padding: 10px;
}

.custom-data-table td {
  padding: 12px; 
  text-align: center;
  color: #333; 
}

.custom-data-table tr:nth-child(odd) {
  background-color: #f0f0f0; 
}

.custom-data-table tr:nth-child(even) {
  background-color: #f886fc; 
}

.custom-data-table tr:hover {
  background: linear-gradient(135deg, #9f4ff3, #5994fc);
  transition: background-color 0.3s ease; 
}

.bottoms {
  margin-top: 20px;
  padding: 20px;
}
</style>
