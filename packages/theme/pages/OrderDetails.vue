<template>
  <div>
    <div class="top-bar">
      <div @clic="goBack" class="sf-chevron--left sf-chevron icon_back">
        <span class="sf-search-bar__icon">
          <SfIcon color="var(--c-primary)" size="20px" icon="chevron_left" />
        </span>
      </div>
      <div class="header-push">Order Details</div>
      <SfButton
        v-if="isTrackingAvailable"
        class="sf-button--pure top-button"
        @click="openTrackModal = true"
      >
        <div class="color-def">Track Order</div>
      </SfButton>
    </div>

    <div v-if="enableLoader" key="loadingCircle" class="loader-circle">
      <LoadingCircle :enable="enableLoader" />
    </div>

    <div v-if="!enableLoader" class="details">
      <div class="provider-head p-0">
        <div class="provide-img">
          <img
            :src="
              cartGetters.getProviderImage(
                cartGetters.getBppProvider(order.cart)
              )
                ? cartGetters.getProviderImage(
                    cartGetters.getBppProvider(order.cart)
                  )
                : require('~/assets/images/store-placeholder.png')
            "
          />
        </div>
        <div class="p-name">
          {{
            providerGetters.getProviderName(
              cartGetters.getBppProvider(order.cart)
            )
          }}
        </div>
        <div class="text-padding">
          <!-- <span class="p-distance"> by </span> {{ order.cart.bpp.descriptor.name }} -->
        </div>

        <!-- <div class="text-padding">
            <div class="flexy-center">
              <div class="p-name">Abc</div>
              <div class="text-padding">
                <span class="p-distance"> by </span>
                <span>BCA</span>
              </div>
            </div>
          </div> -->
      </div>

      <div class="sub-heading">
        <div class="p-name">Items</div>
      </div>

      <div
        :key="index + 'new'"
        v-for="(product, index) in cartGetters.getItems(order.cart)"
        class="checkout-product"
      >
        <div class="s-p-image">
          <SfImage
            :src="cartGetters.getItemImage(product)"
            alt="product img"
            :width="85"
            :height="90"
          />
        </div>
        <div class="s-p-details">
          <div class="s-p-name">{{ cartGetters.getItemName(product) }}</div>
          <div class="s-p-weight">x {{ cartGetters.getItemQty(product) }}</div>
          <div class="s-p-price">
            ₹ {{ cartGetters.getItemPrice(product).regular }}
          </div>
        </div>
      </div>

      <div class="sub-heading"></div>

      <Card>
        <SfAccordion>
          <SfAccordionItem :header="'Shipping'">
            <AddressCard
              :name="order.shippingAddress.name"
              :address="order.shippingAddress.address"
              :mobile="order.shippingAddress.mobile"
              :building="order.shippingAddress.building"
              :pincode="order.shippingAddress.pincode"
            />
          </SfAccordionItem>
        </SfAccordion>
      </Card>

      <div class="sub-heading"></div>

      <Card>
        <SfAccordion>
          <SfAccordionItem :header="'Billing'">
            <CardContent v-if="order.shippingAsBilling" class="flex-space-bw">
              <div class="address-text">Same as Shipping Details</div>
            </CardContent>
            <AddressCard
              v-else
              :name="order.billingAddress.name"
              :address="order.billingAddress.address"
              :mobile="order.billingAddress.mobile"
              :building="order.billingAddress.building"
              :pincode="order.billingAddress.pincode"
            />
          </SfAccordionItem>
        </SfAccordion>
      </Card>

      <div class="sub-heading"></div>

      <Card>
        <SfAccordion>
          <SfAccordionItem :header="'Payment'">
            <!-- <CardContent class="flex-space-bw">
              <div class="address-text">SubTotal</div>
              <div class="address-text">
                ₹{{ cartGetters.getTotals(order.cart).total }}
              </div>
            </CardContent>
            <CardContent class="flex-space-bw">
              <div class="address-text">Delivery Charges</div>
              <div class="address-text">₹0.00</div>
            </CardContent>
            <CardContent class="flex-space-bw">
              <div class="address-text">Taxes (CGST)</div>
              <div class="address-text">₹0.00</div>
            </CardContent>
            <CardContent class="flex-space-bw">
              <div class="address-text">Taxes(SGST)</div>
              <div class="address-text">₹0.00</div>
            </CardContent> -->
            <CardContent
              v-for="breakup in order.cart.quote.breakup"
              :key="breakup.title"
              class="flex-space-bw"
            >
              <div class="address-text">{{ breakup.title }}</div>
              <div class="address-text">₹{{ breakup.price.value }}</div>
            </CardContent>
            <div><hr class="sf-divider divider" /></div>
            <CardContent class="flex-space-bw">
              <div class="address-text bold">Total</div>
              <div class="address-text bold">
                ₹{{ order.cart.quote.price.value }}
              </div>
            </CardContent>
            <CardContent class="flex-space-bw">
              <div class="address-text">Method</div>
              <div class="address-text">{{ order.paymentMethod }}</div>
            </CardContent>
            <CardContent class="flex-space-bw">
              <div class="address-text">Status</div>
              <div class="address-text">{{ order.order.payment.status }}</div>
            </CardContent>
            <CardContent v-if="false" class="flex-space-bw">
              <div class="address-text">Transaction Id</div>
              <div class="address-text">
                <!-- {{ order.order.payment.params.transaction_id }} -->
              </div>
            </CardContent>
          </SfAccordionItem>
        </SfAccordion>
      </Card>

      <div class="sub-heading">
        <!-- <div class="p-name">Order</div> -->
      </div>
      <Card>
        <SfAccordion>
          <SfAccordionItem :header="'Order'">
            <CardContent class="flex-space-bw">
              <div class="address-text">ID</div>
              <div class="address-text order-id">{{ order.order.id }}</div>
            </CardContent>
            <CardContent class="flex-space-bw">
              <div class="address-text">Placed at</div>
              <div class="address-text">{{ order.order.created_at }}</div>
            </CardContent>
            <CardContent v-if="isFulfillmentAvailable" class="flex-space-bw">
              <div class="address-text">Status</div>
              <div class="address-text">{{ isFulfillmentAvailable.state }}</div>
            </CardContent>
          </SfAccordionItem>
        </SfAccordion>
      </Card>

      <div class="sub-heading">
        <!-- <div class="p-name">Order</div> -->
      </div>
      <div v-if="isFulfillmentAvailable">
        <Card>
          <SfAccordion>
            <SfAccordionItem :header="'Fulfillment'">
              <CardContent
                v-if="fulfillmentData.fulfillment.state"
                class="flex-space-bw"
              >
                <div class="address-text">State</div>
                <div class="address-text">
                  {{ fulfillmentData.fulfillment.state.descriptor.name }}
                </div>
              </CardContent>
              <CardContent
                v-if="fulfillmentData.fulfillment.id"
                class="flex-space-bw"
              >
                <div class="address-text">Id</div>
                <div class="address-text">
                  {{ fulfillmentData.fulfillment.id }}
                </div>
              </CardContent>
              <CardContent
                v-if="fulfillmentData.provider.descriptor"
                class="flex-space-bw"
              >
                <div class="address-text">Provider</div>
                <div class="address-text">
                  {{ fulfillmentData.provider.descriptor.name }}
                </div>
              </CardContent>
              <!-- <CardContent class="flex-space-bw">
                <div class="address-text">Status</div>
                <div class="address-text">{{isFulfillmentAvailable.state}}</div>
              </CardContent> -->
            </SfAccordionItem>
          </SfAccordion>
        </Card>
        <div class="fulfillment-progress" v-if="false">
          <div class="head">
            <span>Fulfillment Progress</span>
          </div>
          <div class="sub-head">
            <img src="/icons/calendar.svg" alt="" />
            <span>ETA</span>
            <span class="time">Today, 1.30pm</span>
          </div>
          <div
            class="track-details"
            :class="{
              first: index === 0,
              last: index === fulfillmentSteps.length - 1,
            }"
            v-for="(step, index) in fulfillmentSteps"
            :key="index"
          >
            <div class="check-container">
              <div v-if="index !== 0" class="dot"></div>
              <div v-if="index !== 0" class="dot"></div>
              <div class="check"><img src="/icons/check.svg" alt="" /></div>
              <div
                v-if="index !== fulfillmentSteps.length - 1"
                class="dot"
              ></div>
              <div
                v-if="index !== fulfillmentSteps.length - 1"
                class="dot"
              ></div>
            </div>
            <div class="step-details">
              <div class="step-name">{{ step.status }}</div>
              <div class="step-time">{{ step.time }}</div>
            </div>
          </div>
        </div>
      </div>
      <button
        class="sf-button color-primary support-btns card-checkbox"
        @click="goHome"
      >
        <div class="f-btn-text">Home</div>
      </button>
      <button
        v-if="isSupportAvailable"
        class="sf-button color-primary support-btns"
        @click="openSupportModal = true"
      >
        <div class="f-btn-text">Contact Support</div>
        <img class="btn-img" src="/icons/support.svg" />
      </button>
      <button
        v-if="false"
        class="color-light sf-button cancel-order-btn"
        @click="onCancel"
      >
        <div class="btn-text">Cancel Order</div>
      </button>
      <ModalSlide :visible="openSupportModal" @close="openSupportModal = false">
        <div class="modal-heading">Contact Support</div>
        <div><hr class="sf-divider" /></div>
        <div class="modal-body">
          <div class="support-text">
            You can reach out to one of our customer support executives for any
            help, queries or feedback to
            {{
              providerGetters.getProviderName(
                cartGetters.getBppProvider(order.cart)
              )
            }}
          </div>
          <SfButton
            class="support-btns"
            @click="openWindow('tel:' + isSupportAvailable.phone)"
            aria-label="Close modal"
            type="button"
            >Call us</SfButton
          >
          <SfButton
            class="support-btns"
            @click="openWindow('mailto:' + isSupportAvailable.email)"
            aria-label="Close modal"
            type="button"
            >Email us</SfButton
          >
          <SfButton
            class="support-btns"
            @click="openWindow(isSupportAvailable.uri)"
            aria-label="Close modal"
            type="button"
            >Chat with us</SfButton
          >
        </div>
      </ModalSlide>

      <ModalSlide :visible="openTrackModal" @close="openTrackModal = false">
        <div class="modal-heading">Track</div>
        <div><hr class="sf-divider" /></div>
        <div class="modal-body">
          <div v-if="!isTrackingAvailable" class="support-text">
            No Tracking details available
            <!-- {{
              providerGetters.getProviderName(
                cartGetters.getBppProvider(order.cart)
              )
            }} -->
          </div>
          <div v-else>
            <SfButton
              class="support-btns"
              aria-label="Close modal"
              type="button"
              @click="openWindow(isTrackingAvailable)"
              >open Link</SfButton
            >
          </div>
          <!-- <SfButton class="support-btns" aria-label="Close modal" type="button"
            >Call us</SfButton
          >
          <SfButton class="support-btns" aria-label="Close modal" type="button"
            >Chat with us</SfButton
          > -->
        </div>
      </ModalSlide>
    </div>
  </div>
</template>
<script>
import {
  SfSteps,
  SfSidebar,
  SfButton,
  SfModal,
  SfCheckbox,
  SfAccordion,
  SfImage,
  SfInput,
  SfIcon,
} from '@storefront-ui/vue';
import ModalSlide from '~/components/ModalSlide.vue';
import LoadingCircle from '~/components/LoadingCircle';
import SfAccordionItem from '~/components/Accordion.vue';
import AddressInputs from '~/components/AddressInputs.vue';
import Footer from '~/components/Footer.vue';
import {
  cartGetters,
  providerGetters,
  useTrack,
  useOrderStatus,
  useSupport,
} from '@vue-storefront/beckn';

import {
  ref,
  onBeforeMount,
  computed,
  onBeforeUnmount,
} from '@vue/composition-api';
import Card from '~/components/Card.vue';
import CardContent from '~/components/CardContent.vue';

import ProductCard from '~/components/ProductCard';
import AddressCard from '~/components/AddressCard';

export default {
  name: 'OrderDetails',
  components: {
    SfButton,
    SfSteps,
    SfSidebar,
    SfModal,
    SfCheckbox,
    Footer,
    ModalSlide,
    SfInput,
    Card,
    SfImage,
    CardContent,
    ProductCard,
    AddressInputs,
    SfAccordion,
    SfAccordionItem,
    SfIcon,
    LoadingCircle,
    AddressCard,
  },
  setup(_, context) {
    // const isThankYou = computed(() => currentStep.value === 'thank-you');

    const order = ref(null);
    const enableLoader = ref(true);
    const fulfillmentData = ref(null);
    const {
      poll: onTrack,
      init: track,
      pollResults: trackResult,
    } = useTrack('track');
    const {
      poll: onSupport,
      init: support,
      pollResults: supportResult,
    } = useSupport('support');

    const {
      poll: onStatus,
      init: status,
      pollResults: statusResult,
      stopPolling: stopStatusPolling,
    } = useOrderStatus('status');
    const isTrackingAvailable = computed(() => {
      return trackResult.value?.message?.tracking?.url;
    });

    const isFulfillmentAvailable = computed(() => {
      fulfillmentData.value = statusResult.value?.message?.order;
      return statusResult.value?.message?.order;
    });
    const isSupportAvailable = computed(() => {
      return supportResult.value?.message;
    });
    const transactionId = context.root.$route.query.id;
    const fulfillmentStep = [
      { status: 'Items Packed', time: 'May 2021, 2021 12:40 PM' },
      { status: 'Delivery agent assigned', time: 'May 2021, 2021 12:40 PM' },
      { status: 'Agent enroute to store', time: 'May 2021, 2021 12:40 PM' },
      null,
      null,
      null,
      null,
    ];
    const fulfillmentSteps = [
      { status: 'Items Packed', time: 'May 2021, 2021 12:40 PM' },
      { status: 'Delivery agent assigned', time: 'May 2021, 2021 12:40 PM' },
      { status: 'Agent enroute to store', time: 'May 2021, 2021 12:40 PM' },
    ];
    const openSupportModal = ref(false);
    const openTrackModal = ref(false);
    const goHome = () => context.root.$router.push('/');
    const goBack = () => context.root.$router.push('/orders');
    const onCancel = () => context.root.$router.push('/cancelorder');

    const callSupport = async () => {
      const params = {
        context: {
          // eslint-disable-next-line camelcase
          transaction_id: order.value.transactionId,
          // eslint-disable-next-line camelcase
          bpp_id: order.value.cart.bpp.id,
        },
        message: {
          // eslint-disable-next-line camelcase
          ref_id: order.value.order.id,
        },
      };

      try {
        const response = await support(params);
        await onSupport({ messageId: response.context.message_id });
      } catch (error) {
        console.log('Error calling support apis - ', error);
      }
    };

    const callStatus = async () => {
      const params = {
        context: {
          // eslint-disable-next-line camelcase
          transaction_id: order.value.transactionId,
          // eslint-disable-next-line camelcase
          bpp_id: order.value.cart.bpp.id,
        },
        message: {
          // eslint-disable-next-line camelcase
          order_id: order.value.order.id,
        },
      };

      try {
        const response = await status(params);
        await onStatus({ messageId: response.context.message_id });
      } catch (error) {
        console.log('Error calling status apis - ', error);
      }
    };

    const callTrack = async () => {
      const params = {
        context: {
          // eslint-disable-next-line camelcase
          transaction_id: order.value.transactionId,
          // eslint-disable-next-line camelcase
          bpp_id: order.value.cart.bpp.id,
        },
        message: {
          // eslint-disable-next-line camelcase
          order_id: order.value.order.id,
        },
      };

      try {
        const response = await track(params);
        await onTrack({ messageId: response.context.message_id });
      } catch (error) {
        console.log('Error calling track apis - ', error);
      }
    };
    onBeforeMount(async () => {
      const orders = JSON.parse(localStorage.getItem('orderHistory')) ?? [];

      order.value = orders.find((ord) => {
        return ord.transactionId === transactionId;
      });
      await callTrack();
      await callSupport();
      await callStatus();
      enableLoader.value = false;
    });

    onBeforeUnmount(async () => {
      stopStatusPolling();
    });

    const openWindow = (link) => {
      // debugger
      window.open(link);
    };
    return {
      goHome,
      goBack,
      order,
      cartGetters,
      isTrackingAvailable,
      providerGetters,
      fulfillmentStep,
      fulfillmentSteps,
      openSupportModal,
      onCancel,
      enableLoader,
      openTrackModal,
      callTrack,
      trackResult,
      openWindow,
      isFulfillmentAvailable,
      isSupportAvailable,
      fulfillmentData,
    };
  },
};
</script>

<style lang="scss" scoped>
// .header-top{
//     position: fixed;
//     width: 100%;
//     top: 45px;
//     z-index: 9;
// }
.support-btns {
  width: 100%;
  border-radius: 3px;
}

.cancel-order-btn {
  width: 100%;
  border-radius: 3px;
}

.bold {
  font-weight: 600;
}

.card-checkbox {
  margin-bottom: 20px;
}

.sf-checkbox__checkmark:hover {
  --checkbox-border-color: transparent !important;
  border: 0;
}
.sf-input {
  input {
    font-size: 14px;
    &:placeholder {
      font-size: 14px;
    }
  }
}

.s-p-weight {
  margin-top: 6px;
  font-size: 14px;
  color: #8a8d8e;
}

.flex-space-bw {
  justify-content: space-between;
}

.details {
  padding: 20px;
  height: calc(100vh - 149px);
  overflow: scroll;
}

.address-bar-icon {
  margin: 10px 10px 1px 10px;
}

.address-text {
  align-self: center;
  margin-left: 6px;
}

.sub-heading {
  margin: 14px 0px;
  display: flex;
  justify-content: space-between;
}

.footer {
  position: fixed;
  bottom: 0;
}

.icon_back {
  position: absolute;
  left: 0;
  margin: 10px;
}

.color-def {
  color: #f37a20;
}

.top-bar {
  align-items: center;
  display: flex;
  font-size: 18px;
  justify-content: space-around;
  height: 60px;
  font-weight: 500;
  background: white;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.07);
}

.p-name {
  font-size: 16px;
  font-weight: 600;
  color: #37474f;
}
#checkout {
  box-sizing: border-box;
  @include for-desktop {
    max-width: 1240px;
    margin: 0 auto;
  }
}
.checkout-product:first-child {
  border-top: 0px solid rgba(0, 0, 0, 0.3);
}
.checkout-product {
  display: flex;
  margin-top: 15px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.3);
  padding-bottom: 15px;
  &:first-child(2) {
    border-top: 0 !important;
  }
  .s-p-image {
    margin-right: 25px;
  }
  .s-p-name {
    font-size: 11px;
    color: #000;
  }
  .s-p-price {
    font-size: 16px;
    font-weight: 600;
    margin-top: 10px;
    color: #f37a20;
  }
}
.checkout {
  @include for-desktop {
    display: flex;
  }
  &__main {
    @include for-desktop {
      flex: 1;
      padding: var(--spacer-xl) 0 0 0;
    }
  }
  &__aside {
    @include for-desktop {
      flex: 0 0 25.5rem;
      margin: 0 0 0 4.25rem;
    }
  }
  &__steps {
    --steps-content-padding: 0 var(--spacer-base);
    @include for-desktop {
      --steps-content-padding: 0;
    }

    &-auth::v-deep .sf-steps__step:first-child {
      --steps-step-color: #e8e4e4;
    }
  }
}
.modal-heading {
  margin: 20px;
  font-size: 20px;
  font-weight: 500;
}

.color-def {
  color: #f37a20;
}

.top-button {
  position: absolute;
  right: 6vw;
}
.modal-body {
  padding: 28px;
  .support-text {
    font-size: 15px;
  }
  .support-btns {
    margin-top: 20px;
    width: 100%;
  }
}

.sf-loader {
  top: 40px;
}

.order-id {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
