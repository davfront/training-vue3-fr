<template>
  <ul class="filters">
    <li>
      <a
        href="#"
        :class="{ selected: filter === 'all' }"
        @click.prevent="filter = 'all'"
      >
        All
      </a>
    </li>
    <li>
      <a
        href="#"
        :class="{ selected: filter === 'active' }"
        @click.prevent="filter = 'active'"
      >
        Active
      </a>
    </li>
    <li>
      <a
        href="#"
        :class="{ selected: filter === 'completed' }"
        @click.prevent="filter = 'completed'"
      >
        Completed
      </a>
    </li>
  </ul>
</template>

<script>
export default {
  props: {
    modelValue: {
      type: String,
      default: 'all'
    }
  },
  data() {
    return {
      filter: 'all'
    }
  },
  watch: {
    modelValue: {
      immediate: true,
      handler(newValue) {
        this.filter = newValue
      }
    },
    filter(newValue) {
      this.$emit('update:modelValue', newValue)
    }
  }
}
</script>

<style lang="scss" scoped>
.filters {
  margin: 0;
  padding: 0;
  list-style: none;
  position: absolute;
  right: 0;
  left: 0;

  li {
    display: inline;

    a {
      color: inherit;
      margin: 3px;
      padding: 3px 7px;
      text-decoration: none;
      border: 1px solid transparent;
      border-radius: 3px;

      &:hover {
        border-color: rgba(175, 47, 47, 0.1);
      }

      &.selected {
        border-color: rgba(175, 47, 47, 0.2);
      }
    }
  }
}
</style>
