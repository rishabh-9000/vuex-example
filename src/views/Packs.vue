<template>
  <div class="main">
    <div class="pack">
      <h2>PACKAGES</h2>
      <div class="pack-info">
        <md-card class="md-primary" v-for="pack in packs" :key="pack.name">
          <div>
            <md-card-header>
              <div class="md-title">{{ pack.name.toUpperCase() }}</div>
            </md-card-header>

            <md-card-content>
              Channels:
              <li v-for="channel in pack.channels" :key="channel">
                {{ channel }}
              </li>
              Monthly Price: {{ pack.price }}
            </md-card-content>
          </div>

          <md-card-actions>
            <md-button @click="subscribe(pack)">Subscribe</md-button>
          </md-card-actions>
        </md-card>
      </div>
    </div>

    <div class="channel">
      <h2>CHANNELS</h2>
      <div class="channel-info">
        <md-card
          class="md-primary"
          v-for="channel in channels"
          :key="channel.name"
        >
          <div>
            <md-card-header>
              <div class="md-title">{{ channel.name }}</div>
            </md-card-header>

            <md-card-content> Price: {{ channel.price }} </md-card-content>
          </div>

          <md-card-actions>
            <md-button>Add</md-button>
          </md-card-actions>
        </md-card>
      </div>
    </div>

    <div class="service">
      <h2>SERVICE</h2>
      <div class="service-info">
        <md-card
          class="md-primary"
          v-for="service in services"
          :key="service.name"
        >
          <div>
            <md-card-header>
              <div class="md-title">{{ service.name }}</div>
            </md-card-header>

            <md-card-content> Price: {{ service.price }} </md-card-content>
          </div>

          <md-card-actions>
            <md-button>Add</md-button>
          </md-card-actions>
        </md-card>
      </div>
    </div>

    <md-dialog-prompt
      :md-active.sync="subscribeDialog"
      v-model="numberOfMonths"
      md-title="Enter the Number of Months"
      md-input-placeholder="Number of Months"
      md-confirm-text="OK"
      @md-confirm="getSubscriptionAmount"
    />

    <md-dialog :md-active.sync="confirmDialog">
      <md-dialog-title>Confirm</md-dialog-title>
      <h4>
        You have successfully subscribed the following packs: {{ pack.name }}
      </h4>
      <h4>Monthly Price: {{ pack.price }}</h4>
      <h4>No. of Months: {{ numberOfMonths }}</h4>
      <h4>Subscription Amount: {{ subscriptionAmount }}</h4>

      <md-dialog-actions>
        <md-button class="md-primary" @click="confirmDialog = false"
          >Cancel</md-button
        >
        <md-button class="md-primary" @click="confirmSubscribe"
          >Confirm</md-button
        >
      </md-dialog-actions>
    </md-dialog>
    <md-snackbar md-position="center" :md-active.sync="emailNotification">
      <span>Email Notification Sent Succesfully</span>
    </md-snackbar>
  </div>
</template>

<script>
export default {
  name: "Packs",

  data() {
    return {
      pack: Object,
      subscriptionAmount: undefined,
      subscribeDialog: false,
      confirmDialog: false,
      numberOfMonths: undefined,
      emailNotification: false
    };
  },

  computed: {
    user() {
      return this.$store.state.user;
    },

    packs() {
      return this.$store.state.packs;
    },

    channels() {
      return this.$store.state.channels;
    },

    services() {
      return this.$store.state.services;
    }
  },

  methods: {
    subscribe(pack) {
      this.pack = pack;
      if (pack.price <= this.user.balance) {
        this.subscribeDialog = true;
      } else {
        alert("Not Enought Balance");
        return;
      }
    },

    getSubscriptionAmount() {
      this.subscriptionAmount = this.pack.price * this.numberOfMonths;
      this.confirmDialog = true;
    },

    confirmSubscribe() {
      if (this.pack.price * this.numberOfMonths > this.user.balance) {
        this.confirmDialog = false;
        alert("Insufficient Balance");
        return;
      }
      let payload = this.pack;
      payload.numberOfMonths = this.numberOfMonths;
      this.$store.dispatch("confirmSubscribe", payload);
      this.confirmDialog = false;
      this.emailNotification = true;
    }
  }
};
</script>

<style lang="scss">
.pack-info,
.channel-info,
.service-info {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
}

.pack-info .md-card,
.channel-info .md-card,
.service-info .md-card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 350px;
}
</style>
