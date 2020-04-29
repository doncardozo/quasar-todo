<template>
  <q-layout view="lHh Lpr lFf">
    <q-header>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />

        <q-toolbar-title>
          Todo
        </q-toolbar-title>

        <div>{{todaysDate}}</div>
      </q-toolbar>
      
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above :width="200" :breakpoint="600">
      <q-scroll-area
        style="height: calc(100% - 192px); margin-top: 192px; border-right: 1px solid #ddd"
      >
        <q-list padding>
          <!-- <q-item-label
              header
              class="text-grey-8">
              Menú
          </q-item-label>-->
          <EssentialLink 
            v-for="link in essentialLinks" 
            :key="link.title" 
            v-bind="link" />
        </q-list>
      </q-scroll-area>

      <q-img
        class="absolute-top"
        src="https://cdn.quasar.dev/img/material.png"
        style="height: 177px"
      >
        <div class="absolute-bottom bg-transparent">
          <q-avatar size="56px" class="q-mb-sm">
            <img src="https://cdn.quasar.dev/img/boy-avatar.png" />
          </q-avatar>
          <div class="text-weight-bold">Tony Cardozo</div>
          <div>@kr2o</div>
        </div>
      </q-img>
    </q-drawer>

    <q-page-container>
      <keep-alive>
        <router-view />
      </keep-alive>
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from "components/EssentialLink";
import { date } from "quasar";

export default {
  name: "MainLayout",

  components: {
    EssentialLink
  },

  data() {
    return {
      leftDrawerOpen: false,
      essentialLinks: [
        {
          title: "Todo",
          caption: "Todo List",
          icon: "list",
          link: "/"
        },
        {
          title: "Help",
          caption: "Help Section",
          icon: "help",
          link: "/help"
        }
      ]
    };
  },
  computed: {
    todaysDate() {
      let timeStamp = Date.now();
      return date.formatDate(timeStamp, "dddd D MMMM");
    }
  }
};
</script>

<style lang="scss">
.header-image {
  height: 100%;
  z-index: 1;
  opacity: 0.2;
  filter: grayscale(100%);
}
</style>