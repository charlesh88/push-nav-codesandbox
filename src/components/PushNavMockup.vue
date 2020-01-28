<template>
  <div id="main" class="l-main">
    <nav id="nav" class="l-nav">
      <div class="c-nav-item c-nav-item--ancestors" v-for="item in ancestors">
        <div class="c-nav-item__tree-up" @click="setCurNav(item.id)">&lt;</div>
        <div class="c-nav-item__label">{{ item.label }}</div>
      </div>

      <div
        class="c-nav-item c-nav-item--siblings"
        v-for="item in siblings"
        :class="{'c-nav-item--center': item.id === curNavLocId, 'c-nav-item--has-kids': hasKids(item) && !item.id === curNavLocId}"
      >
        <div class="c-nav-item__tree-up"></div>
        <div class="c-nav-item__label" @click="setCurNav(item.id)">{{ item.label }}</div>
        <div class="c-nav-item__tree-down" @click="setCurNav(item.id)"></div>
      </div>

      <div
        class="c-nav-item c-nav-item--children"
        v-for="item in children"
        :class="{'c-nav-item--center': item.id === curNavLocId, 'c-nav-item--has-kids': hasKids(item)}"
      >
        <div class="c-nav-item__tree-up"></div>
        <div class="c-nav-item__label" @click="setCurNav(item.id)">{{ item.label }}</div>
        <div class="c-nav-item__tree-down" @click="setCurNav(item.id)">&gt;</div>
      </div>
    </nav>
    <div class="l-view" v-bind="mainview">This is l-view {{ mainview.label }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      curNavLocId: "plots",
      curViewId: this.curNavLocId,
      navArray: [
        { id: "myitems", label: "My Items", parent: "root" },
        { id: "layouts", label: "Layouts", parent: "myitems" },
        { id: "plots", label: "Plots", parent: "myitems" },
        { id: "tables", label: "Tables", parent: "myitems" },
        { id: "overlay-plot-dmx", label: "DMX Overlay Plot", parent: "plots" },
        { id: "overlay-plot-aaa", label: "AAA Overlay Plot", parent: "plots" },
        { id: "telem-dmx-4001", label: "DMX-4001", parent: "overlay-plot-dmx" },
        { id: "telem-dmx-4002", label: "DMX-4002", parent: "overlay-plot-dmx" },
        { id: "telem-dmx-4003", label: "DMX-4003", parent: "overlay-plot-dmx" }
      ]
    };
  },
  computed: {
    ancestors() {
      let tree = [];
      let currentItem = this.getItem(this.curNavLocId);
      while (currentItem && currentItem.parent !== "root") {
        currentItem = this.navArray.find(
          item => item.id === currentItem.parent
        );
        tree.push(currentItem);
      }
      return tree.reverse();
    },
    children() {
      return this.sortAlphabetically(
        this.navArray.filter(
          item => item.parent === this.curNavLocId && item.id
        )
      );
    },
    // returns array of self plus siblings
    // if self has children, do not return any siblings
    siblings() {
      const hasChildren = !!this.navArray.find(
        item => item.parent === this.curNavLocId
      );
      const currentItem = this.getItem(this.curNavLocId);

      if (hasChildren) {
        return [currentItem];
      }
      return this.sortAlphabetically(
        this.navArray.filter(item => item.parent === currentItem.parent)
      );
    },
    mainview() {
      return this.getItem(this.curViewId);
    }
  },
  methods: {
    setCurNav(navLocId) {
      this.curNavLocId = navLocId;
    },
    getItem(id) {
      return this.navArray.find(item => item.id === id);
    },
    sortAlphabetically(objects) {
      return objects.sort((a, b) => (a.label > b.label ? 1 : -1));
    },
    hasKids(node) {
      return !!this.navArray.find(item => item.parent === node.id);
    }
  }
};
</script>
