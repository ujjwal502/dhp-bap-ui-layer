<template>
  <div class="s-product">
    <div :class="{'horizontal':horizontalView, 'vertical': !horizontalView}">
      <div @click="$emit('goToProduct')" class="s-p-image">
        <SfImage :src="_pImage" alt="product img" :width="horizontalView ? 85 : 75" :height="90" />
      </div>
      <div @click="$emit('goToProduct')" class="s-p-details">
        <div class="s-p-name">{{ _pName }}</div>
        <!-- <div class="s-p-weight">{{ _pWieght }}</div> -->
        <div class="price-increase"  v-if="!!_updatedPrice && _updatedPrice !== _pPrice">Price increased by <span>₹{{_updatedPrice-_pPrice}}</span></div>
        <div class="s-p-price" v-if="_updatedCount !== 0">₹ {{ _updatedPrice ? _updatedPrice : _pPrice }}</div>
        <span class="out-stock"  v-if="_updatedCount === 0">Out of Stock</span>
      </div>
      <div class="s-p-add-cart">
        <SfImage v-if="deleteCard" src="/icons/delete.svg" alt="delete-icon" @click="$emit('deleteItem')"/>
        <div v-if="dropdownCouner" class="dropdown-container d-flex ">
          <span class="avail-unit" v-if="!!_updatedCount && _updatedCount !== _pCount ">{{_updatedCount}} units are available</span>
          <div class="position-relative">
          <div class="dropdown-button" v-if="_updatedCount !== 0" @click="openDropdown=!openDropdown">
            <div>{{_pCount}}</div>
            <SfIcon icon="chevron_down" size="xxs" />
          </div>
          <div class="dowpdown" v-if="openDropdown">
            <div class="dowpdown-item" :class="{'border':index !== count.length-1,'color-text':count==='More'}" v-for="(count, index) in dpList" :key="index" @click="dropdownClick(count)">{{count}}</div>
          </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { SfImage, SfIcon } from '@storefront-ui/vue';
import AddToCart from './AddToCart.vue';
import { productGetters } from '@vue-storefront/beckn';
import { ref, computed } from '@vue/composition-api';

export default {
  name: 'ProductCard',
  components: {
    SfImage,
    AddToCart,
    SfIcon
  },
  props: {
    product: {type: Object},
    pName: { type: String, default: '' },
    pWieght: { type: String, default: '' },
    pPrice: { type: Number, default: '' },
    pImage: { type: String, default: '' },
    pCount: { type: Number, default: 0 },
    updatedPrice: { type: Number, default: null },
    updatedCount: { type: Number, default: null },
    horizontalView: { type: Boolean, default: true },
    deleteCard: { type: Boolean, default: false},
    dropdownCouner: {type: Boolean, default: false}
  },
  setup(props, {emit}) {
    const _pName = computed(() => props.pName);
    const _pWieght = computed(() => props.pWieght);
    const _pPrice = computed(() => props.pPrice);
    const _pImage = computed(() => props.pImage);
    const _pCount = computed(() => props.pCount);
    const _updatedPrice = computed(() => props.updatedPrice);
    const _updatedCount = computed(() => props.updatedCount);
    const dpList = [1, 2, 3, 4, 'More'];
    const openDropdown = ref(false);

    const dropdownClick = (data) => {
      console.log(data);
      if (data === 'More') {
        emit('dropdownMore');
        openDropdown.value = false;
      } else {
        emit('updateItemCount', data);
        openDropdown.value = false;
      }
    };

    return {
      productGetters,
      _pName,
      _pWieght,
      _pPrice,
      _pImage,
      _pCount,
      dpList,
      openDropdown,
      _updatedPrice,
      _updatedCount,
      dropdownClick
    };
  },
  methods: {
    getProductBase(product) {
      this.$emit('removeSearchResults');
      return btoa(JSON.stringify(product));
    }
  }
};
</script>
<style lang="scss" scoped>
.dropdown-container{
  position: relative;
  .dropdown-button{
    display: flex;
    align-items: center;
    justify-content: space-around;
    color: #F37A20;
    font-weight: 700;
    width: 70px;
    height: 30px;
    border: 1px solid #D9D9D9;
    box-sizing: border-box;
    border-radius: 6px;
  }

  .dowpdown{
    background: #FFFFFF;
    box-shadow: 0px 4px 14px rgba(0, 0, 0, 0.3);
    border-radius: 6px;
    padding: 0 7px;
    position: absolute;
    width: 56px;
    z-index: 1;
    .dowpdown-item{
      display: flex;
      justify-content: center;
      padding: 7px 0;
    }
    .border{
      border-bottom: 1px solid rgba(226, 226, 226, 0.7);
    }
    .color-text{
      color: #F37A20;
      cursor: pointer;
    }
  }
}

</style>
