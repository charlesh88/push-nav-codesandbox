<template>
  <div id="main" class="l-main">
    <div class="l-wrapper--nav">
      <nav id="nav" class="c-nav">
        <div class="c-nav__section c-nav--ancestors">
          <div
            class="c-nav__item c-nav__item--ancestor"
            v-for="(item, index) in ancestors"
            :key="item.id"
            :class="{ 'c-nav__item--in-view': item.id === viewId }"
            @click="setView(item.id)"
          >
            <div class="c-nav__item__tree-up" @click.stop="setNav(item.id)"></div>
            <div
              class="c-nav__item__label"
              :style="{ marginLeft: index * navMarginPx + 'px' }"
            >{{ item.label }}</div>
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
            @click="setView(item.id)"
          >
            <div class="c-nav__item__tree-up"></div>
            <div
              class="c-nav__item__label"
              :style="{ marginLeft: ancestors.length * navMarginPx + 'px' }"
            >{{ item.label }}</div>
            <div class="c-nav__item__tree-down" @click.stop="setNav(item.id)"></div>
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
            'c-nav__item--has-kids': hasKids(item)
            }"
            @click="setView(item.id)"
          >
            <div class="c-nav__item__tree-up"></div>
            <div
              class="c-nav__item__label"
              :style="{ marginLeft: ancestors.length * navMarginPx + navMarginPx + 'px' }"
            >{{ item.label }}</div>
            <div class="c-nav__item__tree-down" @click.stop="setNav(item.id)"></div>
          </div>
        </div>
      </nav>
    </div>
    <object-view :object="mainview" @sync-nav="setNav"></object-view>
  </div>
</template>

<script>
import data from "../assets/data.json";
import ObjectView from "./ObjectView";

const STARTING_ID = "myitems";
const AUTO_UPDATE_NAV_ON_CLICK = false;

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
      navArray: data,
      navMarginPx: 10,
      autoUpdateNavOnItemClick: false
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
    this.$on("sync-nav", this.object.id);
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
    setNav(navLocId) {
      this.navId = navLocId;
      console.log("setNav", this.viewId);
    },
    setView(id) {
      this.viewId = id;
      console.log(this.viewId);
      if (AUTO_UPDATE_NAV_ON_CLICK) {
        let currentItem = this.getItem(id);
        this.navId = this.hasKids(currentItem) ? id : currentItem.parentId;
      }
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
