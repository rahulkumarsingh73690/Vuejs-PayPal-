<template>
  <span>
    <v-chip
      label
      color="blue"
      text-color="white"
      x-small
      class="mb-3"
      @click="pro = true"
      @click.stop="installpaypal"
    >
      Upgrade
    </v-chip>

    <v-dialog v-model="pro" max-width="400">
      <v-card shaped elevation="8">
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn icon @click="pro = false"><v-icon>mdi-close</v-icon></v-btn>
        </v-card-actions>
        <div class="ml-6 mr-6">
          <div v-if="!paidFor">
            <div class="title mb-3">
              <h2 class="mb-1 text-center font-weight-medium">
                GoOnlineTools Pro
              </h2>
              <h3 class="mb-1 mt-3 text-center font-weight-medium">
                ${{ this.product.price }}/Year
              </h3>
            </div>
            <v-card outlined>
              <v-card-subtitle>Features</v-card-subtitle
              ><v-card-text
                ><ul class="ml-4 mb-3">
                  <li>No Ads</li>
                  <li>100% uptime</li>
                  <li>Quick support</li>
                  <li>Fast response</li>
                  <li>Access premium tools</li>
                  <li>Hosted on high quality server</li>
                </ul>
              </v-card-text></v-card
            >
            <br />
            <div ref="paypal"></div>
          </div>

          <div v-if="paidFor">
            <div class="title mb-3">
              <h2 class="mb-1 text-center font-weight-medium">
                GoOnlineTools Pro
              </h2>
              <h3 class="mb-1 mt-3 text-center font-weight-medium">
                Activated
              </h3>
            </div>
            <v-card outlined>
              <v-card-subtitle>Features</v-card-subtitle
              ><v-card-text
                ><ul class="ml-4 mb-3">
                  <li>No Ads</li>
                  <li>100% uptime</li>
                  <li>Quick support</li>
                  <li>Fast response</li>
                  <li>Access premium tools</li>
                  <li>Hosted on high quality server</li>
                </ul>
              </v-card-text></v-card
            >
            <div class="text-center mx-auto">
              <v-btn class="mt-4 mb-4" disabled
                >Thanks for choosing pro plan</v-btn
              >
            </div>
          </div>
        </div>
      </v-card>
    </v-dialog>
  </span>
</template>

<script>
export default {
  name: 'pro',

  data: function() {
    return {
      pro: false,
      loaded: false,
      paidFor: false,
      product: {
        price: 9.99,
        description: 'GoOnlineTools Premium Subscription for 1 year'
      }
    }
  },
  methods: {
    installpaypal: function() {
      const script = document.createElement('script')
      script.src =
        'https://www.paypal.com/sdk/js?client-id=YOUR-CLIENT-ID'
      script.addEventListener('load', this.setLoaded)
      document.body.appendChild(script)
    },
    setLoaded: function() {
      this.loaded = true
      window.paypal
        .Buttons({
          createOrder: (data, actions) => {
            return actions.order.create({
              purchase_units: [
                {
                  description: this.product.description,
                  amount: {
                    currency_code: 'USD',
                    value: this.product.price
                  }
                }
              ]
            })
          },
          onApprove: async (data, actions) => {
            const order = await actions.order.capture()
            this.paidFor = true
            this.$toast.success('Payment Success...')
            fb.writeSettings('pro', true)
            console.log(order)
          },
          onError: err => {
            console.log(err)
            this.$toast.error('Payment Failed... Please try again')
          }
        })
        .render(this.$refs.paypal)
    }
  }
}
</script>
