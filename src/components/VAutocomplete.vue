<template>
  <div class="autocomplete-wrapper" @click="showDropdown(true)">
    <input
      type="text"
      class="autocomplete-input"
      @keyup.enter="setHighlitedValue"
      @keyup.down="moveHighlited('down')"
      @keyup.up="moveHighlited()"
      @keyup.escape="showDropdown(false)"
      @input="showDropdown(true)"
      :placeholder="placeholder"
      v-model="value"
    />
    <span 
      class="autocomplete-clear-btn"
      v-if="value.length"
      @click="setValue('')"
    >&times;</span>
    <div
      class="autocomplete-dropdown"
      v-if="matchedData.length && !dropdownHidden"
    >
      <div
        v-for="(item, index) in matchedData"
        :class="{ highlited: this.selectedIndex === index }"
        role="option"
        tabindex="0"
        aria-selected="false"
        @click.stop="setValue(item)"
        :key="item"
        class="autocomplete-dropdown-item"
        v-html="wrapMatchedPart(item)"
      ></div>
    </div>
  </div>
</template>


<script>
export default {
  props: {
    data: {
      type: Array,
      required: true,
    },
    modelValue: {
      type: String,
      required: true,
    },
    placeholder: {
      type: String,
      required: false,
    }
  },
  data() {
    return {
      value: this.modelValue,
      selectedIndex: null,
      dropdownHidden: true,
    };
  },
  computed: {
    matchedData() {
      const regExp = new RegExp(this.value, "i");
      return this.data.filter((item) => regExp.test(item));
    },
  },
  methods: {
    showDropdown(isDropdownVisible) {
      if (isDropdownVisible) {
        this.dropdownHidden = false;
      } else {
        this.dropdownHidden = true;
      }
    },
    close(e) {
      if (!this.$el.contains(e.target)) {
        this.showDropdown(false);
      }
    },
    setValue(text) {
      this.value = text;
      this.showDropdown(false);
    },
    wrapMatchedPart(text) {
      const regExp = new RegExp(this.value, "i");
      const matchedPart = text.match(regExp)[0];
      return text.replace(
        regExp,
        `<span style="background-color: rgba(0,0,0,.1)">${matchedPart}</span>`
      );
    },
    setHighlitedValue() {
      if (typeof this.selectedIndex === "number") {
        this.setValue(this.matchedData[this.selectedIndex]);
      }
    },
    moveHighlited(location) {
      if (location === "down") {
        if (
          this.selectedIndex == null ||
          this.selectedIndex >= this.matchedData.length - 1
        ) {
          this.selectedIndex = 0;
        } else {
          this.selectedIndex++;
        }
      } else {
        if (this.selectedIndex == null || this.selectedIndex <= 0) {
          this.selectedIndex = this.matchedData.length - 1;
        } else {
          this.selectedIndex--;
        }
      }
    },
  },
  watch: {
    value() {
      this.selectedIndex = null;
      this.$emit("update:modelValue", this.value);
    },
  },
  mounted() {
    document.addEventListener("click", this.close);
  },
  beforeUnmount() {
    document.removeEventListener("click", this.close);
  },
};
</script>

<style scoped>
.autocomplete-wrapper {
  position: relative;
  border: 1px solid rgba(0, 0, 0, 0.1);
  font-size: 18px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  padding: 10px 20px;
  box-sizing: border-box;
}

.autocomplete-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  text-align: left;
  border-bottom-right-radius: 15px;
  border-bottom-left-radius: 15px;
  overflow: hidden;
  box-shadow: 0px 4px 4px rgba(0, 0, 0, 0.1);
}

.autocomplete-dropdown-item {
  padding: 10px 20px;
  cursor: pointer;
  transition: 0.3s ease;
}
.autocomplete-dropdown-item.highlited {
  background: rgba(0, 0, 0, 0.2);
}

.autocomplete-dropdown-item:hover,
.autocomplete-dropdown-item:focus {
  background-color: rgba(0, 0, 0, 0.1);
}

.autocomplete-clear-btn {
  position: absolute;
  right: 20px;
  top: 5px;
  cursor: pointer;
  font-family: inherit;
  font-size: 1.5em;
  cursor: pointer;
  transition: .3s ease;
}

.autocomplete-clear-btn:hover {
  opacity: .3;
}

.autocomplete-input {
  border: none;
  width: 100%;
  font-family: inherit;
  font-size: inherit;
  outline: none;
}
</style>