<template>
  <div id="main" class="l-main">
    <div class="l-wrapper--nav">
      <nav id="nav" class="l-nav">
        <div
          class="c-nav-item c-nav-item--ancestors"
          v-for="item in ancestors"
          :key="item.id"
          :class="{ 'c-nav-item--in-view': item.id === curViewId }"
        >
          <div class="c-nav-item__tree-up" @click="setCurNav(item.id)"></div>
          <div class="c-nav-item__label" @click="setCurView(item.id)">{{ item.label }}</div>
          <div class="c-nav-item__tree-down"></div>
        </div>

        <div
          class="c-nav-item c-nav-item--siblings"
          v-for="item in siblings"
          :key="item.id"
          :class="{
          'c-nav-item--center': item.id === curNavLocId, 
          'c-nav-item--in-view': item.id === curViewId, 
          'c-nav-item--has-kids': hasKids(item) && !item.id === curNavLocId
          }"
        >
          <div class="c-nav-item__tree-up"></div>
          <div class="c-nav-item__label" @click="setCurView(item.id)">{{ item.label }}</div>
          <div class="c-nav-item__tree-down" @click="setCurNav(item.id)"></div>
        </div>

        <div
          class="c-nav-item c-nav-item--children"
          v-for="item in children"
          :key="item.id"
          :class="{
          'c-nav-item--center': item.id === curNavLocId, 
          'c-nav-item--in-view': item.id === curViewId, 
          'c-nav-item--has-kids': hasKids(item)}"
        >
          <div class="c-nav-item__tree-up"></div>
          <div class="c-nav-item__label" @click="setCurView(item.id)">{{ item.label }}</div>
          <div class="c-nav-item__tree-down" @click="setCurNav(item.id)"></div>
        </div>
      </nav>
    </div>
    <object-view :object="mainview"></object-view>
  </div>
</template>

<script>
import ObjectView from "./ObjectView";

const STARTING_ID = "myitems";
export default {
  name: "PushNavMockup",
  components: {
    ObjectView: ObjectView
  },
  data() {
    return {
      curNavLocId: undefined,
      curViewId: undefined,
      navArray: [
        { id: "myitems", label: "My Items", parentId: "root" },
        { id: "layouts", label: "Layouts", parentId: "myitems" },
        { id: "plots", label: "Plots", parentId: "myitems" },
        { id: "tables", label: "Tables", parentId: "myitems" },
        {
          id: "overlay-plot-dmx",
          label: "DMX Overlay Plot",
          parentId: "plots"
        },
        {
          id: "overlay-plot-aaa",
          label: "AAA Overlay Plot",
          parentId: "plots"
        },
        {
          id: "telem-dmx-4001",
          label: "DMX-4001",
          parentId: "overlay-plot-dmx"
        },
        {
          id: "telem-dmx-4002",
          label: "DMX-4002",
          parentId: "overlay-plot-dmx"
        },
        {
          id: "telem-dmx-4003",
          label: "DMX-4003",
          parentId: "overlay-plot-dmx"
        },
        { id: "dash-rtsci", label: "RT SCI Dashboard", parentId: "layouts" },
        { id: "dash-nirvss", label: "NIRVSS Dashboard", parentId: "layouts" },
        { id: "dash-flight", label: "Flight Dashboard", parentId: "layouts" },
        {
          id: "ovw-roversys",
          label: "Rover Sys Overview",
          parentId: "layouts"
        },
        { id: "alarms", label: "Alarms", parentId: "tables" },
        { id: "thermal", label: "Thermal", parentId: "tables" },
        { id: "power", label: "Power", parentId: "tables" },
        { id: "comm", label: "Comm", parentId: "tables" },
        { id: "alarms-red", label: "Red Alarms", parentId: "alarms" },
        { id: "alarms-yellow", label: "Yellow Alarms", parentId: "alarms" },
        { id: "telem-thrm-5203", label: "THRM-5203", parentId: "thermal" },
        { id: "telem-thrm-5204", label: "THRM-5204", parentId: "thermal" },
        { id: "telem-thrm-5205", label: "THRM-5205", parentId: "thermal" },
        { id: "telem-thrm-5206", label: "THRM-5206", parentId: "thermal" },
        { id: "telem-thrm-5207", label: "THRM-5207", parentId: "thermal" },
        { id: "telem-pwr-1002", label: "PWR-1002", parentId: "power" },
        { id: "telem-pwr-1003", label: "PWR-1003", parentId: "power" },
        { id: "telem-pwr-1004", label: "PWR-1004", parentId: "power" },
        { id: "telem-pwr-1005", label: "PWR-1005", parentId: "power" },
        { id: "telem-pwr-1006", label: "PWR-1006", parentId: "power" },
        { id: "telem-pwr-1007", label: "PWR-1007", parentId: "power" },
        { id: "telem-pwr-1008", label: "PWR-1008", parentId: "power" },
        { id: "telem-pwr-1009", label: "PWR-1009", parentId: "power" },
        { id: "telem-pwr-1010", label: "PWR-1010", parentId: "power" },
        { id: "telem-pwr-1011", label: "PWR-1011", parentId: "power" },
        { id: "telem-pwr-1012", label: "PWR-1012", parentId: "power" },
        { id: "telem-pwr-1013", label: "PWR-1013", parentId: "power" },
        { id: "telem-pwr-1014", label: "PWR-1014", parentId: "power" },
        { id: "telem-pwr-1015", label: "PWR-1015", parentId: "power" },
        { id: "telem-pwr-1016", label: "PWR-1016", parentId: "power" },
        { id: "telem-pwr-1017", label: "PWR-1017", parentId: "power" },
        { id: "telem-pwr-1018", label: "PWR-1018", parentId: "power" },
        { id: "telem-pwr-1019", label: "PWR-1019", parentId: "power" },
        { id: "telem-pwr-1020", label: "PWR-1020", parentId: "power" },
        { id: "telem-pwr-1021", label: "PWR-1021", parentId: "power" },
        { id: "telem-pwr-1022", label: "PWR-1022", parentId: "power" },
        { id: "telem-pwr-1023", label: "PWR-1023", parentId: "power" },
        { id: "telem-pwr-1024", label: "PWR-1024", parentId: "power" },
        { id: "telem-pwr-1025", label: "PWR-1025", parentId: "power" },
        { id: "telem-pwr-1026", label: "PWR-1026", parentId: "power" },
        { id: "telem-pwr-1027", label: "PWR-1027", parentId: "power" },
        { id: "telem-pwr-1028", label: "PWR-1028", parentId: "power" },
        { id: "telem-pwr-1029", label: "PWR-1029", parentId: "power" },
        { id: "telem-pwr-1030", label: "PWR-1030", parentId: "power" },
        {
          id: "telem-aaa-0009",
          label: "AAA-0009",
          parentId: "overlay-plot-aaa"
        },
        {
          id: "telem-aaa-0010",
          label: "AAA-0010",
          parentId: "overlay-plot-aaa"
        },
        {
          id: "telem-aaa-0011",
          label: "AAA-0011",
          parentId: "overlay-plot-aaa"
        },
        {
          id: "telem-aaa-0012",
          label: "AAA-0012",
          parentId: "overlay-plot-aaa"
        },
        {
          id: "telem-aaa-0013",
          label: "AAA-0013",
          parentId: "overlay-plot-aaa"
        },
        {
          id: "telem-aaa-0014",
          label: "AAA-0014",
          parentId: "overlay-plot-aaa"
        },
        {
          id: "telem-aaa-0015",
          label: "AAA-0015",
          parentId: "overlay-plot-aaa"
        }
      ]
    };
  },
  mounted() {
    this.curViewId = this.curNavLocId = STARTING_ID;
  },
  computed: {
    ancestors() {
      let tree = [];
      let currentItem = this.getItem(this.curNavLocId);
      while (currentItem && currentItem.parentId !== "root") {
        currentItem = this.navArray.find(
          item => item.id === currentItem.parentId
        );
        tree.push(currentItem);
      }
      return tree.reverse();
    },
    children() {
      return this.sortAlphabetically(
        this.navArray.filter(
          item => item.parentId === this.curNavLocId && item.id
        )
      );
    },
    // returns array of self plus siblings
    // if self has children, do not return any siblings
    siblings() {
      const hasChildren = !!this.navArray.find(
        item => item.parentId === this.curNavLocId
      );
      const currentItem = this.getItem(this.curNavLocId);

      if (hasChildren) {
        return [currentItem];
      }
      return this.sortAlphabetically(
        this.navArray.filter(item => item.parentId === currentItem.parentId) // This is the parentId that is haivng problems
      );
    },
    mainview() {
      return this.curViewId ? this.getItem(this.curViewId) : {};
    }
  },
  methods: {
    setCurNav(navLocId) {
      this.curNavLocId = navLocId;
    },
    setCurView(id) {
      let currentItem = this.getItem(id);
      this.curViewId = id;
      this.curNavLocId = this.hasKids(currentItem) ? id : currentItem.parentId;
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
