<template>
<base-click-outside :do=close>
  <div class="search-select relative">
    <button ref="button" 
            type="button" 
            class="w-full p-2 border rounded bg-white text-grey-dark text-left"
            @click="open">
      <span v-if="value!==''">{{value}}</span>
      <slot v-else>Please select a brand</slot>
    </button>
    <div v-show="isOpen" 
         class="search-select-dropdown mt-1 rounded p-2 bg-grey-darkest w-full" 
         ref="dropdown">
      <input type="text"  
             ref="search"
             class="search-select-search w-full rounded p-2 bg-grey-darker"
             v-model="search"
             @keydown.up="highlightPrev"
             @keydown.down="highlightNext"
             @keydown.esc="close"
             @keydown.tab.prevent
             @keydown.enter="selectHighlighted">
      <ul ref="options"
          class="search-select-options p-0 mt-2 text-white overflow-y-scroll h-64">
        <li class="search-select-option p-2 hover:bg-grey-dark rounded" 
            :class="{ 'bg-blue': index === highlightedIndex}"
            v-for="(option, index) in filteredOptions" 
            :key="index"
            @click="select(option)">
          {{ option }}
        </li>
      </ul>
    </div>
  </div>
  </base-click-outside>
</template>

<script>
import Popper from "popper.js";
import BaseClickOutside from "./base-click-outside";

export default {
  components: {
    BaseClickOutside
  },
  props: {
    value: {
      type: String,
      default: ""
    },
    options: {
      type: Array,
      default() {
        return [];
      }
    },
    filterFunction: {
      type: Function,
      default() {
        return (search, options) => {
          return options.filter(
            option.toLowerCase().startsWith(search.toLowerCase())
          );
        };
      }
    }
  },
  data() {
    return {
      isOpen: false,
      search: "",
      highlightedIndex: 0
    };
  },
  computed: {
    filteredOptions() {
      return this.filterFunction(this.search, this.options);
    }
  },
  methods: {
    open() {
      if (this.isOpen) {
        return;
      }
      this.isOpen = true;
      this.$nextTick(() => {
        this.$refs.search.focus();
        this.setupPopper();
        this.scrollToHighlighted();
      });
    },
    close() {
      if (!this.isOpen) {
        return;
      }
      this.isOpen = false;
      this.$nextTick(() => {
        this.$refs.button.focus();
      });
    },
    highlight() {
      if (this.highlightedIndex < 0) {
        this.highlightedIndex = this.filteredOptions.length - 1;
      }

      if (this.highlightedIndex >= this.filteredOptions.length) {
        this.highlightedIndex = 0;
      }

      this.scrollToHighlighted();
    },
    scrollToHighlighted() {
      this.$refs.options.children[this.highlightedIndex].scrollIntoView({
        block: "nearest"
      });
    },
    highlightNext() {
      this.highlight(++this.highlightedIndex);
    },
    highlightPrev() {
      this.highlight(--this.highlightedIndex);
    },
    selectHighlighted() {
      this.select(this.filteredOptions[this.highlightedIndex]);
    },
    select(option) {
      this.$emit("input", option);
      this.search = "";
      this.highlightedIndex = 0;
      this.close();
    },
    setupPopper() {
      if (this.popper === undefined) {
        this.popper = new Popper(this.$refs.button, this.$refs.dropdown, {
          placement: "bottom-start"
        });
      } else {
        this.popper.scheduleUpdate();
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.search-select {
  ul {
    list-style: none;
    text-align: left;

    &::-webkit-scrollbar {
      width: 6px;
      background-color: #3d4852;
      border-radius: 5px;
    }

    &::-webkit-scrollbar-thumb {
      background-color: rgba(201, 199, 199, 0.867);
      border-radius: 5px;
      height: 10px;
    }
  }
}
</style>
