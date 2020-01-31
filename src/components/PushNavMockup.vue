<template>
  <div id="main" class="l-main">
    <div class="l-wrapper--nav">
      <nav id="nav" class="l-nav">
        <div class="c-nav__section c-nav--ancestors">
          <div
            class="c-nav__item c-nav__item--ancestor"
            v-for="item in ancestors"
            :key="item.id"
            :class="{ 'c-nav__item--in-view': item.id === viewId }"
          >
            <div class="c-nav__item__tree-up" @click="setNav(item.id)"></div>
            <div class="c-nav__item__label" @click="setView(item.id)">{{ item.label }}</div>
            <div class="c-nav__item__tree-down"></div>
          </div>
        </div>

        <div class="c-nav__section c-nav--siblings">
          <div
            class="c-nav__item c-nav__item--sibling"
            v-for="item in siblings"
            :key="item.id"
            :class="{
            'c-nav__item--center': item.id === navId, 
            'c-nav__item--in-view': item.id === viewId, 
            'c-nav__item--has-kids': hasKids(item) && !item.id === navId
            }"
          >
            <div class="c-nav__item__tree-up"></div>
            <div class="c-nav__item__label" @click="setView(item.id)">{{ item.label }}</div>
            <div class="c-nav__item__tree-down" @click="setNav(item.id)"></div>
          </div>
        </div>

        <div class="c-nav__section c-nav--children" :class="moveInNav">
          <div
            class="c-nav__item c-nav__item--child"
            v-for="item in children"
            :key="item.id"
            :class="{
            'c-nav__item--center': item.id === navId, 
            'c-nav__item--in-view': item.id === viewId, 
            'c-nav__item--has-kids': hasKids(item)}"
          >
            <div class="c-nav__item__tree-up"></div>
            <div class="c-nav__item__label" @click="setView(item.id)">{{ item.label }}</div>
            <div class="c-nav__item__tree-down" @click="setNav(item.id)"></div>
          </div>
        </div>
      </nav>
    </div>
    <object-view :object="mainview"></object-view>
  </div>
</template>

<script>
import data from "../assets/data.json";
import ObjectView from "./ObjectView";

const STARTING_ID = "myitems";

export default {
  name: "PushNavMockup",
  components: {
    ObjectView: ObjectView
  },
  data() {
    return {
      navId: undefined,
      viewId: undefined,
      moveInNav: "",
      navArray: data
    };
  },
  watch: {
    navId(newId, oldId) {
      const item = this.getItem(oldId);
      item.parentId === newId
        ? this.transitionNav("up")
        : this.transitionNav("down");
    }
  },
  mounted() {
    this.viewId = this.navId = STARTING_ID;
  },
  computed: {
    ancestors() {
      let tree = [];
      let currentItem = this.getItem(this.navId);
      while (currentItem && currentItem.parentId !== "root") {
        currentItem = this.navArray.find(
          // eslint-disable-next-line no-loop-func
          item => item.id === currentItem.parentId
        );
        tree.push(currentItem);
      }
      return tree.reverse();
    },
    children() {
      return this.sortAlphabetically(
        this.navArray.filter(item => item.parentId === this.navId)
      );
    },
    // returns array of self plus siblings
    // if self has children, do not return any siblings
    siblings() {
      const hasChildren = !!this.navArray.find(
        item => item.parentId === this.navId
      );
      const currentItem = this.getItem(this.navId);

      if (hasChildren) {
        return [currentItem];
      }
      return this.sortAlphabetically(
        this.navArray.filter(item => item.parentId === currentItem.parentId) // This is the parentId that is haivng problems
      );
    },
    mainview() {
      return this.viewId ? this.getItem(this.viewId) : {};
    }
  },
  methods: {
    setNav(navLocId, direction) {
      this.navId = navLocId;
    },
    setView(id) {
      let currentItem = this.getItem(id);
      this.viewId = id;
      this.navId = this.hasKids(currentItem) ? id : currentItem.parentId;
    },
    transitionNav(direction) {
      // direction is either up or down in the tree
      this.moveInNav = direction === "up" ? "slide-right" : "slide-left";
      setTimeout(this.transitionNavClear, 500);
    },
    transitionNavClear() {
      this.moveInNav = "";
    },
    getItem(id) {
      return this.navArray.find(item => item.id === id);
    },
    sortAlphabetically(objects) {
      return objects.sort((a, b) => (a.label > b.label ? 1 : -1));
    },
    hasKids(node) {
      return !!this.navArray.find(item => item.parentId === node.id);
    }
  }
};
</script>
