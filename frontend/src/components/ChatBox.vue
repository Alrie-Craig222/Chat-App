<template>
  <v-container>
    <v-row>
     
      <v-col cols="12" md="4">
        <v-card class="mb-3 gradient-card" elevation="2">
          <v-card-title>
            <div class="headline text-light">Inbox</div>
          </v-card-title>
          <v-card-subtitle>
            <div class="caption text-light">Click to start a convo.</div>
          </v-card-subtitle>
          <v-list>
            <v-list-item-group v-if="filteredUsers.length > 0">
              <v-list-item v-for="user in filteredUsers" :key="user.id" @click="selectReceiver(user)">
                <v-list-item-avatar>
                  <v-img :src="user.avatar" />
                </v-list-item-avatar>
                <v-list-item-content>
                  <v-list-item-title class="text-light">{{ user.name }}</v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list-item-group>
            <v-list-item v-else>
              <v-list-item-content>
                <v-list-item-title class="text-light">No conversations available...</v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-list>
        </v-card>
      </v-col>

      
      <v-col cols="12" md="8">
        <v-card v-if="selectedReceiver" class="pa-3 gradient-card" elevation="2">
          <v-card-title>
            <v-avatar class="mr-3">
              <v-img :src="selectedReceiver.avatar" />
            </v-avatar>
            <div>
              <div class="headline text-light">{{ selectedReceiver.name }}</div>
              <div class="caption text-light">Chat with {{ selectedReceiver.name }}</div>
            </div>
            <router-link class="d-flex justify-end" to="/dashboard" style="text-decoration: none;">
              <v-btn color="primary"><v-icon>mdi-arrow-left</v-icon></v-btn>
            </router-link>
          </v-card-title>

          <v-card-subtitle>
            <v-scroll-y class="message-list">
              <v-list>
                <v-list-item-group v-if="messages && messages.length > 0">
                  <v-list-item v-for="message in messages" :key="message.id">
                    <v-list-item-content>
                      <v-list-item-title class="text-light">
                        <span>{{ message.content }}</span>
                      </v-list-item-title>
                    </v-list-item-content>
                  </v-list-item>
                </v-list-item-group>
                <v-list-item v-else>
                  <v-list-item-content>
                    <v-list-item-title class="text-light">No messages yet...</v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
              </v-list>
            </v-scroll-y>
          </v-card-subtitle>

          
          <v-card-actions>
            <v-textarea v-model="newMessage" label="Type your message" rows="2" outlined dense />
            <v-btn @click="sendMessage" :disabled="!newMessage" style="background-color:blue; color:white;">Send</v-btn>
          </v-card-actions>
        </v-card>


       
        <v-card v-if="replies && replies.length > 0" class="pa-3 mt-3 gradient-card" elevation="2">
          <v-card-title>
            <div class="headline text-light">Replies</div>
          </v-card-title>
          <v-scroll-y class="reply-list">
            <v-list>
              <v-list-item v-for="reply in replies" :key="reply.id">
                <v-list-item-content>
                  <v-list-item-title class="text-light">
                    <span>{{ reply.content }}</span>
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-scroll-y>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      user: {
        id: null,
        name: '',
        avatar: '',
      },
      users: [],
      selectedReceiver: null,
      newMessage: '',
      messages: [], 
      replies: [], 
    };
  },
  created() {
    this.fetchUserData();
    this.fetchUsers();
    this.startMessagePolling();
  },
  computed: {
    filteredUsers() {
      return this.users.filter(user => user.id !== this.user.id);
    }
  },
  methods: {
    async fetchUserData() {
      try {
        const userResponse = await axios.get('http://localhost:8000/api/me/', {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('accessToken')}`,
          },
        });
        this.user = userResponse.data;
        this.fetchMessages();
      } catch (error) {
        console.error('Error fetching user data:', error);
      }
    },
    async fetchUsers() {
      try {
        const response = await axios.get('http://127.0.0.1:8000/api/users/', {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('accessToken')}`,
          },
        });
        if (response.data.status === 'success' && Array.isArray(response.data.data.users)) {
          this.users = response.data.data.users;
        }
      } catch (error) {
        console.error('Error fetching users:', error);
      }
    },
    selectReceiver(user) {
      this.selectedReceiver = user;
      this.fetchMessages();
      this.replies = []; 
    },
    async fetchMessages() {
      if (!this.selectedReceiver) return;
      try {
        const receiverId = this.selectedReceiver.id;

        const response = await axios.get('http://127.0.0.1:8000/chat/api/messages-by-receiver/', {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('accessToken')}`,
          },
          params: {
            receiver_id: receiverId,
          },
        });

        this.messages = response.data;
        this.fetchReplies(); 
      } catch (error) {
        console.error('Error fetching messages:', error);
      }
    },
    async fetchReplies() {
      if (!this.selectedReceiver) return;
      try {
        const receiverId = this.selectedReceiver.id;

        const response = await axios.get('http://127.0.0.1:8000/chat/api/messages-by-sender/', {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('accessToken')}`,
          },
          params: {
            sender_id: receiverId, 
          },
        });

        this.replies = response.data; 
      } catch (error) {
        console.error('Error fetching replies:', error);
      }
    },
    startMessagePolling() {
     
      setInterval(() => {
        if (this.selectedReceiver) {
          this.fetchMessages();
          this.fetchReplies();
        }
      }, 5000);
    },
    async sendMessage() {
      if (!this.selectedReceiver || !this.newMessage) return;

      const messageData = {
        sender: this.user.id,
        receiver: this.selectedReceiver.id,
        content: this.newMessage,
        timestamp: new Date().toISOString(),
      };

      try {
        const response = await axios.post('http://localhost:8000/chat/api/messages/', messageData, {
          headers: {
            Authorization: `Bearer ${localStorage.getItem('accessToken')}`,
            'Content-Type': 'application/json',
          },
        });

        this.messages.push({
          id: response.data.id,
          sender_name: this.user.name,
          content: this.newMessage,
        });

        this.newMessage = '';
      } catch (error) {
        console.error('Error sending message:', error);
      }
    }
  }
};
</script>

<style scoped>
.headline {
  font-size: 1.5em;
  font-weight: bold;
  font-family: "monospace";
}

.caption {
  font-size: 0.9em;
  color: white;
}

.selected-card {
  background-color: #f0f0f0;
  border: 1px solid #1976d2;
  color: #151515;
}

.v-list-item {
  cursor: pointer;
}

.v-card-actions {
  display: flex;
  align-items: center;
}

.v-textarea {
  flex-grow: 1;
  margin-right: 10px;
}

.v-btn {
  align-self: flex-start;
}
.message-list, .reply-list {
  max-height: 100px; 
  overflow-y: auto;
}


.gradient-card {
  background: linear-gradient(135deg, #6a11cb, #2575fc); 
  color: white; 
  border-radius: 10px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.2);
}
</style>
