<template>
  <form
    class="search-form"
    :action="this.$router.resolve({ name: 'Search', query: urlQuery }).href"
    @submit="handleSubmit"
    method="GET"
  >
    <fieldset>
      <legend class="visually-hidden">{{ this.fluent('search') }}</legend>
      <div class="search-form__fields">
        <label for="search-query" class="visually-hidden">{{
          this.fluent('search_input')
        }}</label>
        <!-- TODO: fix placeholder fluent. search 'searchFormLabel' and replace with fluent in all code -->
        <input
          type="text"
          id="search-query"
          name="query"
          v-model="searchQuery"
          class="search-form__input"
          ref="searchQueryField"
          @keyup="handleKeyUp"
          :placeholder="searchFormLabel"
        />
        <button
          type="button"
          v-if="searchQuery && searchQuery.length > 0"
          @click="clearQuery"
          class="search-form__clear-button"
        >
          <Icon id="x" :width="20" :height="20" />
          <span class="visually-hidden">{{
            this.fluent('search_input', 'clear')
          }}</span>
        </button>
        <button type="submit" class="search-form__submit">
          <Icon id="search" :width="18" :height="18" />
          <span class="visually-hidden">{{ this.fluent('search') }}</span>
        </button>
      </div>
    </fieldset>
  </form>
</template>

<script>
import Icon from '@/components/ui/Icon.vue';

export default {
  name: 'SearchForm',
  props: {
    searchFormLabel: String,
    searchFormHandler: Function,
    searchFormKeyUpHandler: {
      type: Function,
      default: null,
    },
    initialQuery: String,
  },
  components: {
    Icon,
  },
  watch: {
    query(queryString) {
      this.searchQuery = queryString;
    },
  },
  methods: {
    handleSubmit(event) {
      event.preventDefault();
      if (!this.searchQuery.length > 0) {
        this.$refs.searchQueryField.focus();
      } else {
        this.$emit('close-search-form');
      }
      this.searchFormHandler(this.searchQuery, this.who);
    },
    handleKeyUp(event) {
      if (!this.searchFormKeyUpHandler) {
        return;
      }
      event.preventDefault();
      if (!this.searchQuery.length > 0) {
        this.$refs.searchQueryField.focus();
      } else {
        this.$emit('close-search-form');
      }
      this.searchFormKeyUpHandler(this.searchQuery, this.who);
    },
    clearQuery() {
      this.searchQuery = '';
      this.$refs.searchQueryField.focus();
      this.$emit('clear-query');
    },
  },
  computed: {
    query: {
      get() {
        return this.$route.query.query || null;
      },
    },
    who() {
      return this.$route.query.who || this.scope.isStaff ? 'staff' : 'all';
    },
    urlQuery() {
      if (this.query) {
        return { query: this.query, who: this.who };
      }
      return { who: this.who };
    },
  },
  data() {
    return {
      searchQuery: this.initialQuery || '',
    };
  },
  mounted() {
    this.$refs.searchQueryField.focus();
  },
};
</script>

<style>
.search-form {
  margin-right: auto;
  width: 100%;
  max-width: 31em;
  position: relative;
}

.search-form fieldset {
  border: 0;
  padding: 0;
}

.search-form__fields {
  display: flex;
  flex-direction: row;
  position: relative;
}

.search-form__fields:focus .search-form__input,
.search-form__fields:hover .search-form__input {
  border: 1px solid var(--blue-60);
}

.search-form__fields:focus button[type='submit'],
.search-form__fields:hover button[type='submit'] {
  border-right: 1px solid var(--blue-60);
}

.search-form__fields input,
.search-form__fields button[type='submit'] {
  font-size: 1.15em;
}

.search-form__input {
  border: 1px solid var(--gray-30);
  width: 100%;
  background-color: var(--white);
  -webkit-appearance: none;
  appearance: none;
  border-radius: 0;
  padding: 0.5em 1.75em 0.5em 3em;
}

.search-form__input::placeholder {
  text-align: center;
}

.search-form__submit {
  border: 0;
  border-right: 1px solid var(--gray-30);
  appearance: none;
  background-color: var(--white);
  width: 2.5em;
  padding: 0.5em;
  margin-right: -1px;
  position: absolute;
  left: 1px;
  top: 1px;
  bottom: 1px;
  transition: background-color 0.2s ease-in-out;
}
.search-form__submit:hover {
  background-color: var(--gray-20);
}
.search-form__submit img {
  vertical-align: middle;
  width: 1.15em;
}
.search-form__clear-button {
  background-color: var(--white);
  position: absolute;
  border: 0;
  top: 3px;
  right: 1px;
  bottom: 1px;
  width: 2em;
  padding: 0;
  line-height: 1;
}

@media (min-width: 57.5em) {
  .search-form__clear-button {
    width: 3em;
  }
}
.search-form__clear-button:hover {
  color: var(--blue-60);
}

.search-form--small {
  padding: 1em;
  max-width: none;
  background: var(--white);
  margin-bottom: 1em;
  position: fixed;
  left: 0;
  right: 0;
  top: 5em;
  z-index: var(--layerTopBar);
  box-shadow: var(--shadowCard);
}
</style>
