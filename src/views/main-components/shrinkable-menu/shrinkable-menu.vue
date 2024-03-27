<style lang="scss" scoped>
@import "./styles/menu.scss";
</style>
<template>
  <div class="ivu-shrinkable-menu">
    <!-- 一级菜单 -->
    <Menu ref="sideMenu" width="80px" theme="dark"  :active-name="currNav" @on-select="selectNav">
      <MenuItem v-for="(item, i) in navList" :key="i" :name="item.name">
        {{item.title}}
      </MenuItem>
    </Menu>
    <!-- 二级菜单 -->
    <Menu
      ref="childrenMenu"
      :active-name="$route.name"
      width="100px"
      @on-select="changeMenu"
    >
      <template v-for="item in menuList">
        <MenuGroup :title="item.title" :key="item.id" style="padding-left:0;">
          <MenuItem :name="menu.name" v-for="menu in item.children" :key="menu.name">
            {{menu.title}}
          </MenuItem>
        </MenuGroup>

      </template>
    </Menu>
  </div>
</template>

<script>
import util from "@/libs/util.js";
export default {
  name: "shrinkableMenu",
  data(){
    return {
      legalNavListTitle: [
        // "商品",
        // "订单",
        // "财务",
        // "营销",
        // "统计",
        "设置",
        "消息",
      ],
      legalMenuList: [
        "店铺管理",
        "系统消息"
      ],
      legalMenuItemList: [
        "店铺设置",
        "系统消息"
      ]
    }
  },
  computed: {

    // 二级菜单列表
    menuList() {
      let newArr = this.$store.state.app.menuList.filter( r => this.legalMenuList.indexOf(r.title) > -1 );
      newArr.forEach((arr) =>{
        let newChildren = arr.children.filter(r => this.legalMenuItemList.indexOf(r.title) > -1 );
        arr.children = newChildren
      });
      // return this.$store.state.app.menuList
      return newArr;
    },
    // 一级菜单
    navList() {
      let newArr = this.$store.state.app.navList.filter( r => this.legalNavListTitle.indexOf(r.title) > -1 );
      return newArr;
      // return  this.$store.state.app.navList
    },
    // 当前一级菜单
    currNav() {
      return this.$store.state.app.currNav;
    }
  },
  watch: {
    // 监听路由变化
    $route: {
      handler: function (val, oldVal) {
        if (val.meta.firstRouterName && val.meta.firstRouterName !== this.currNav) {
          this.selectNav(val.meta.firstRouterName)
        }
      }
    }
  },
  methods: {
    changeMenu(name) { //二级路由点击
      this.$router.push({
        name: name
      });
    },
    selectNav(name) { // 一级路由点击
      this.$store.commit("childrenMenu",this.$refs.childrenMenu)
      this.$store.commit("setCurrNav", name);
      this.setStore("currNav", name);
      util.initRouter(this);
    },
  }
};
</script>
<style lang="scss" scoped>
.ivu-menu-dark.ivu-menu-vertical .ivu-menu-item-active:not(.ivu-menu-submenu), .ivu-menu-dark.ivu-menu-vertical .ivu-menu-submenu-title-active:not(.ivu-menu-submenu){
    color: $theme_color;
}
</style>
