<script>
export default {
  name: 'TreeItem',
  props: {
    model: Object
  },
  data() {
    return {
      isOpen: false
    }
  },
  computed: {
    isFolder() {
      return this.model.children && this.model.children.length
    }
  },
  methods: {
    toggle() {
      if (this.isFolder) {
        this.isOpen = !this.isOpen;
      }
    },
    changeType() {
      if (!this.isFolder) {
        // Emit an event to notify the parent component about the change
        this.$emit('change-type', this.model);
      }
    },
    addChild() {
      // Emit an event to notify the parent component about adding a child
      this.$emit('add-child', this.model);
    }
  }
}
</script>

<template>
  <li>
    <div
      :class="{ bold: isFolder }"
      @click="toggle"
      @dblclick="changeType">
      {{ model.name }}
      <span v-if="isFolder">[{{ isOpen ? '-' : '+' }}]</span>
    </div>
    <ul v-show="isOpen" v-if="isFolder">
  <TreeItem
    class="item"
    v-for="(model, index) in model.children"
    :key="index"
    :model="model"
    @change-type="handleChangeType"
    @add-child="handleAddChild"
  ></TreeItem>
  <li class="add" @click="addChild">+</li>
</ul>
  </li>
</template>