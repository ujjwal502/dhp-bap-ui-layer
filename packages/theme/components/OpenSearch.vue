<template>
  <div>
    <div class="open-search header-top-space">
      <h3>
        Open <br />
        healthcare
      </h3>
      <h4>for All</h4>
      <p>
        A global marketplace to discover and buy anything you need. Just type
        what you want to buy and we'll take care of the rest.
      </p>
      <div class="position-relative">
        <div class="open-search-input">
          <div
            :class="{
              'dropdown-button': true,
              'dropdown-disabled':
                !selectedLocation.latitude || !selectedLocation.longitude
            }"
            @click="onDropdownHeaderClick"
          >
            <div v-if="selectedSearchByOption === 'Consultation'">
              All
            </div>
            <SfIcon icon="chevron_down" size="xxs" />
          </div>
          <input
            class="search-placeholder"
            v-on:keyup.enter="openSearch"
            v-model="message"
            :valid="false"
            errorMessage="errer"
            type="text"
            :placeholder="searchByPlaceholderMapper[selectedSearchByOption]"
            :disabled="
              !selectedLocation.latitude || !selectedLocation.longitude
            "
            v-e2e="'home-search-input'"
          />
          <SfButton
            class="button-pos sf-button--pure color-primary"
            :class="{
              'is-disabled--button':
                !selectedLocation.latitude || !selectedLocation.longitude
            }"
            @click="openSearch"
            :disabled="
              !selectedLocation.latitude || !selectedLocation.longitude
            "
            v-e2e="'home-search-button'"
          >
            <span class="sf-search-bar__icon">
              <SfIcon color="var(--c-text)" size="18px" icon="search" />
            </span>
          </SfButton>
        </div>

        <div class="dowpdown" v-if="openSearchByDropdown">
          <div
            class="dowpdown-item"
            v-for="(searchBy, key, index) in searchByMapper"
            :key="key"
            @click="onSelectDropdownItem(key)"
            :class="{ border: index !== Object.keys(searchByMapper) - 1 }"
          >
            {{ searchBy }}
          </div>
        </div>
      </div>
      <div class="open-search-input">
        <input
          v-model="symptomsText"
          :valid="false"
          errorMessage="errer"
          type="text"
          placeholder="Enter your symptoms"
          :disabled="!selectedLocation.latitude || !selectedLocation.longitude"
          v-e2e="'home-search-input'"
        />
      </div>
      <div v-if="errorMsg" class="error-msg">Please fill out this field.</div>
    </div>

    <div class="sf-footer">
      <SfFooter class="footer">
        <!-- <p><span>By</span> <img src="../assets/images/p-b-phonepe.png" alt="" /> </p> -->
        <p>
          <span class="powered-by">Powered by</span>
          <img src="../assets/images/beckn-logo.png" alt="" />
        </p>
      </SfFooter>
    </div>
  </div>
</template>
<script>
import { SfButton, SfIcon, SfImage } from '@storefront-ui/vue';
import { useUiState } from '~/composables';
import { SfFooter } from '@storefront-ui/vue';
import { ref } from '@vue/composition-api';

const { selectedLocation } = useUiState();

export default {
  components: {
    SfButton,
    SfIcon,
    SfFooter,
    SfImage
  },

  setup(_, context) {
    const message = ref('');
    const symptomsText = ref('');
    const errorMsg = ref(false);
    const openSearchByDropdown = ref(false);
    const searchByMapper = {
      Consultation: 'Consultation',
      Diagnostics: 'Diagnostics'
    };
    const searchByPlaceholderMapper = {
      Consultation: 'Search by doctor, clinic or hospital name',
      Diagnostics: 'Search by diagnostic service name, diagnostic lab name'
    };
    const selectedSearchByOption = ref('Consultation');
    const onDropdownHeaderClick = () => {
      if (selectedLocation.value.latitude || selectedLocation.value.longitude) {
        openSearchByDropdown.value = !openSearchByDropdown.value;
      }
    };
    const onSelectDropdownItem = (selectedOption) => {
      selectedSearchByOption.value = selectedOption;
      openSearchByDropdown.value = false;
    };
    const openSearch = () => {
      if (message.value) {
        if (errorMsg.value) errorMsg.value = false;
        context.root.$router.push({
          name: 'Search',
          params: {
            searchKey: message.value,
            searchBy: selectedSearchByOption.value
          }
        });
      } else {
        errorMsg.value = true;
      }
    };
    return {
      selectedLocation,
      message,
      errorMsg,
      openSearch,
      openSearchByDropdown,
      onSelectDropdownItem,
      searchByMapper,
      selectedSearchByOption,
      searchByPlaceholderMapper,
      onDropdownHeaderClick,
      symptomsText
    };
  }
};
</script>

<style lang="scss" scoped>
.open-search {
  @media (min-width: 560px) {
    padding-top: 40px;
    width: 50%;
    margin: auto;
  }
  padding: 40px 20px;
  h3 {
    font-size: 40px;
    font-weight: 800;
    color: #f37a20;
    line-height: 45px;
  }
  h4 {
    font-size: 27px;
    font-weight: 800;
    line-height: 30px;
  }
  p {
    font-size: 15px;
    font-weight: 400;
    line-height: 20px;
    color: #7c7c7c;
    margin-bottom: 30px;
  }
  .open-search-input {
    display: flex;
    margin-bottom: 8px;
    position: relative;
    &.disable {
      input {
        border: 1px solid #fff;
      }
      button {
        background: #bfbfbf;
        .sf-icon {
          --icon-color: #fff !important;
        }
      }
    }
    input {
      box-shadow: 0px 10px 24px rgba(0, 0, 0, 0.1);
      border-radius: 6px;
      border: 1px solid transparent;
      padding: 22px 10px;
      width: calc(100% - 22px);
      font-size: 15px;
      font-weight: 700;
      &::placeholder {
        font-size: 14px;
        line-height: 17px;
        color: #dbdbdc;
      }
      &:focus {
        border: 1px solid #f37a20 !important;
      }
    }
    button {
      position: absolute;
      padding: 17px;
      height: 63px;
      top: 0;
      // background: #F37A20;
      border-top-right-radius: 6px;
      border-bottom-right-radius: 6px;
      right: 0;
      .sf-icon {
        --icon-color: #fff !important;
      }
    }
    .search-placeholder {
      padding-left: 85px;
    }
  }
  .error-msg {
    font-size: 14px;
    color: #d12727;
  }
}
.sf-footer {
  text-align: center;
  background: #fbfcff !important;
  position: fixed;
  bottom: 0px;
  width: 100%;
  padding: 0;
  p {
    margin: 0;
    span {
      font-size: 17px;
      position: relative;
      top: -6px;
      &.powered-by {
        font-size: 10px;
        top: -1px !important;
      }
    }
  }
}
.dropdown-button {
  position: absolute;
  border-right: 1px solid #d9d9d9;
  height: 100%;
  padding: 10px;
  width: 75px;
  display: flex;
  align-items: center;
  justify-content: space-around;
  color: #f37a20;
  box-sizing: border-box;
  font-weight: 700;
  cursor: pointer;
}
.dowpdown {
  background: #ffffff;
  box-shadow: 0px 4px 14px rgba(0, 0, 0, 0.3);
  border-radius: 6px;
  padding: 0 7px;
  position: absolute;
  width: 342px;
  z-index: 1;
  .dowpdown-item {
    display: flex;
    align-items: center;
    padding: 8px 0;
    cursor: pointer;
  }
  .border {
    border-bottom: 1px solid rgba(226, 226, 226, 0.7);
  }
  .color-text {
    color: #f37a20;
    cursor: pointer;
  }
}
.search-by-icon {
  padding-right: 20px;
  padding-left: 8px;
}
.dropdown-disabled {
  opacity: 0.4;
  color: #e0e0e1 !important;
  .sf-icon {
    --icon-color: #e0e0e1 !important;
  }
}
</style>
