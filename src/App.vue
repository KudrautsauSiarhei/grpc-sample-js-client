<template>
  <v-app id="app">
    <v-main>
      <v-container grid-list-md fill-height>
        <v-layout align-center justify-center>
          <v-flex xs12>
            <v-layout row wrap>
              <v-flex xs12>
                <VTextField label="Name" v-model="name"/>
              </v-flex>
              <v-flex xs4>
                <VCard>
                  <VCardTitle>Greeting request:</VCardTitle>
                  <VCardText>
                    <VTextarea :value="message"/>
                  </VCardText>
                  <VCardActions>
                    <VSpacer/>
                    <VBtn color="primary" @click="loadMessage">Send greeting request</VBtn>
                  </VCardActions>
                </VCard>
              </v-flex>
              <v-flex xs4>
                <VCard>
                  <VCardTitle>Greetings request:</VCardTitle>
                  <VCardText>
                    <VTextarea :value="messages.join('\n')"/>
                  </VCardText>
                  <VCardActions>
                    <VSpacer/>
                    <VBtn color="primary" @click="loadMessages">Send greetings request</VBtn>
                  </VCardActions>
                </VCard>
              </v-flex>
              <v-flex xs4>
                <VCard>
                  <VCardTitle>Endless greetings request:</VCardTitle>
                  <VCardText>
                    <VTextarea :value="messages2.join('\n')"/>
                  </VCardText>
                  <VCardActions>
                    <VBtn color="warning" @click="cancel">Cancel</VBtn>
                    <VSpacer/>
                    <VBtn color="primary" @click="loadMessagesWithCancellation">Send greetings request</VBtn>
                  </VCardActions>
                </VCard>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import { HelloRequest } from './proto/GreetingService_pb';
import { GreetingServiceClient } from './proto/GreetingService_grpc_web_pb';

export default {
  name: 'App',
  data() {
    return {
      name: 'Siarhei',
      message: '',
      messages: [],
      messages2: [],
    };
  },
  created() {
    this.client = new GreetingServiceClient("http://localhost:8085", null, null);
  },
  methods: {
    loadMessage() {
      const getRequest = new HelloRequest();
      getRequest.setName(this.name);
      this.message = '';
      this.client.greeting(getRequest, {}, (err, response) => {
        this.message = response.getGreeting();
      });
    },
    loadMessages() {
      const getRequest = new HelloRequest();
      getRequest.setName(this.name);
      this.messages = [];
      const stream = this.client.greetings(getRequest, {});
      stream.on('data', (response) => {
        this.messages.push(response.getGreeting());
      });
    },
    cancel() {
      if (this.stream) {
        this.stream.cancel();
      }
    },
    loadMessagesWithCancellation() {
      const getRequest = new HelloRequest();
      getRequest.setName(this.name);
      this.messages2 = [];
      if (this.stream) {
        this.stream.cancel();
      }
      this.stream = this.client.endlessGreetings(getRequest, {});
      this.stream.on('data', (response) => {
        this.messages2.push(response.getGreeting());
      });
      this.stream.on('end', () => {
        this.stream = null;
      });
    },
    clear() {
      this.message = '';
      this.messages = [];
      this.messages2 = [];
    },
  },
}
</script>
