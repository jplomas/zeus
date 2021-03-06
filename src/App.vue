<template>
  <IonApp>
    <IonSplitPane content-id="main-content">
      <ion-menu content-id="main-content" type="overlay">
        <ion-content>
          <ion-button v-bind:fill="fill()">
            <ion-select v-model="sharedState.network" :interface-options="customAlertOptions">
              <ion-select-option value="offline">Offline</ion-select-option>
              <ion-select-option value="testnet">Testnet</ion-select-option>
              <ion-select-option value="mainnet">Mainnet</ion-select-option>
              <ion-select-option value="zond" disabled>Zond Devnet</ion-select-option>
            </ion-select>
          </ion-button>
          <ion-list id="inbox-list">
            <!-- <ion-list-header>ZEUS</ion-list-header>
            <ion-note>The QRL Interface</ion-note> -->
            <ion-menu-toggle auto-hide="false" v-for="(p, i) in appPages" :key="i">
              <ion-item @click="selectedIndex = i" router-direction="root" :router-link="p.url" lines="none" detail="false" class="hydrated" :class="{ selected: selectedIndex === i }">
                <ion-icon slot="start" :ios="p.iosIcon" :md="p.mdIcon"></ion-icon>
                <ion-label>{{ p.title }}</ion-label>
              </ion-item>
            </ion-menu-toggle>
          </ion-list>
          <ion-list v-if="labels.length > 0" id="labels-list">
            <ion-list-header>Bookmarks</ion-list-header>
            <ion-item class="addr" v-for="(label, index) in labels" lines="none" :key="index" router-direction="root" :router-link="label.url">
              <ion-icon slot="start" :ios="bookmarkOutline" :md="bookmarkSharp"></ion-icon>
              <ion-label>{{ label.name }}</ion-label>
            </ion-item>
          </ion-list>
        </ion-content>
      </ion-menu>
      <ion-router-outlet id="main-content"></ion-router-outlet>
    </IonSplitPane>
  </IonApp>
</template>

<script lang="ts">
import { IonApp, IonSelectOption, IonButton, IonSelect, IonContent, IonIcon, IonItem, IonLabel, IonList, IonListHeader, IonMenu, IonMenuToggle, IonRouterOutlet, IonSplitPane } from '@ionic/vue';
import { defineComponent, ref } from 'vue';
import { useRouter, useRoute } from 'vue-router';
import { addOutline, addSharp, bookmarkOutline, bookmarkSharp, lockOpenOutline, lockOpenSharp, homeOutline, homeSharp, globeOutline, globeSharp, cogOutline, cogSharp, hardwareChipOutline, hardwareChipSharp, peopleOutline, peopleSharp } from 'ionicons/icons';
import { Plugins } from '@capacitor/core'
import state from './store';

const { Storage } = Plugins

export default defineComponent({
  name: 'App',
  components: {
    IonApp, 
    IonContent, 
    IonIcon, 
    IonItem, 
    IonLabel, 
    IonList, 
    IonListHeader, 
    IonMenu, 
    IonMenuToggle, 
    // IonNote, 
    IonRouterOutlet, 
    IonSplitPane,
    IonSelectOption,
    IonSelect,
    IonButton
  },
  data () {
    return {
      labels: state.bookmarks,
      sharedState: state
    }
  },
  methods: {
    fill() {
      if (this.sharedState.network !== 'offline') {
        return 'solid';
      }
      return 'outline';
    },
  },
  async created () {
    const getObject = async function() {
      const ret = await Storage.get({ key: 'bookmarks' });
      if (ret.value === null) {
        console.log('No bookmarks yet...')
        return []
      } else {
        console.log('Bookmarks found... fetching')
        return JSON.parse(ret.value)
      }
    }

    const loadBookmarks = async function() {
      const bookmarks = await getObject()
      return bookmarks
    }

    const bookmarks = await loadBookmarks()
    this.sharedState.bookmarks = bookmarks
  
  },
  setup() {
    const router = useRouter()
    const route = useRoute()
    const customAlertOptions: any = {
      header: 'Network',
    }
    const selectedIndex = ref(0);
    const appPages = [
      {
        title: 'Home',
        url: '/home',
        iosIcon: homeOutline,
        mdIcon: homeSharp
      },
      {
        title: 'Explore',
        url: '/explorer',
        iosIcon: globeOutline,
        mdIcon: globeSharp
      },
      {
        title: 'Open',
        url: '/open',
        iosIcon: lockOpenOutline,
        mdIcon: lockOpenSharp
      },
      {
        title: 'New',
        url: '/new',
        iosIcon: addOutline,
        mdIcon: addSharp
      },
      // {
      //   title: 'Tools',
      //   url: '/folder/Tools',
      //   iosIcon: cogOutline,
      //   mdIcon: cogSharp
      // },
      // {
      //   title: 'Contacts',
      //   url: '/folder/Contacts',
      //   iosIcon: peopleOutline,
      //   mdIcon: peopleSharp
      // },
      // {
      //   title: 'Staking',
      //   url: '/folder/Contacts',
      //   iosIcon: hardwareChipOutline,
      //   mdIcon: hardwareChipSharp
      // }
    ];
  

    const path = window.location.pathname.split('/')[1];
    if (path === 'a' || path === 'explorer' || path === 'block' || path === 'tx') { selectedIndex.value = 1 }
    // const path = window.location.pathname.split('folder/')[1];
    // if (path !== undefined) {
    //   selectedIndex.value = appPages.findIndex(page => page.title.toLowerCase() === path.toLowerCase());
    // }
    
    return { 
      selectedIndex,
      appPages, 
      addOutline, 
      addSharp, 
      bookmarkOutline, 
      bookmarkSharp, 
      lockOpenOutline, 
      lockOpenSharp, 
      homeOutline, 
      homeSharp, 
      globeOutline, 
      globeSharp, 
      cogOutline, 
      cogSharp, 
      hardwareChipOutline, 
      hardwareChipSharp,
      peopleOutline,
      peopleSharp,
      router,
      customAlertOptions,
      isSelected: (url: string) => url === route.path ? 'selected' : ''
    }
  },
  watch: {
    'sharedState.bookmarks': function (oldState, newState) {
      this.labels = this.sharedState.bookmarks
    }
  },
});
</script>

<style scoped>

ion-menu ion-content {
  --background: var(--ion-item-background, var(--ion-background-color, #fff));
}

ion-menu.md ion-content {
  --padding-start: 8px;
  --padding-end: 8px;
  --padding-top: 20px;
  --padding-bottom: 20px;
}

ion-menu.md ion-list {
  padding: 20px 0;
}

ion-menu.md ion-note {
  margin-bottom: 30px;
}

ion-menu.md ion-list-header,
ion-menu.md ion-note {
  padding-left: 10px;
}

ion-menu.md ion-list#inbox-list {
  border-bottom: 1px solid var(--ion-color-step-150, #d7d8da);
}

ion-menu.md ion-list#inbox-list ion-list-header {
  font-size: 22px;
  font-weight: 600;

  min-height: 20px;
}

ion-menu.md ion-list#labels-list ion-list-header {
  font-size: 16px;

  margin-bottom: 18px;

  color: #757575;

  min-height: 26px;
}

ion-menu.md ion-item {
  --padding-start: 10px;
  --padding-end: 10px;
  border-radius: 4px;
}

ion-menu.md ion-item.selected {
  --background: rgba(var(--ion-color-primary-rgb), 0.14);
}

ion-menu.md ion-item.selected ion-icon {
  color: var(--ion-color-primary);
}

ion-menu.md ion-item ion-icon {
  color: #616e7e;
}

ion-menu.md ion-item ion-label {
  font-weight: 500;
}

ion-menu.ios ion-content {
  --padding-top: 34px;
  --padding-bottom: 20px;
}

ion-menu.ios ion-list {
  padding: 20px 0 0 0;
}

ion-menu.ios ion-note {
  line-height: 24px;
  margin-bottom: 20px;
}

ion-menu.ios ion-item {
  --padding-start: 16px;
  --padding-end: 16px;
  --min-height: 50px;
}

ion-menu.ios ion-item.selected ion-icon {
  color: var(--ion-color-primary);
}

ion-menu.ios ion-item ion-icon {
  font-size: 24px;
  color: #73849a;
}

ion-menu.ios ion-list#labels-list ion-list-header {
  margin-bottom: 8px;
}

ion-menu.ios ion-list-header,
ion-menu.ios ion-note {
  padding-left: 16px;
  padding-right: 16px;
}

ion-menu.ios ion-note {
  margin-bottom: 8px;
}

ion-note {
  display: inline-block;
  font-size: 16px;

  color: var(--ion-color-medium-shade);
}

ion-item.selected {
  --color: var(--ion-color-primary);
}
.addr {
  transition: opacity 0.3s ease-in-out, color 0.3s ease-in-out;
  cursor: pointer;
}
.addr:hover {
  color: var(--ion-color-primary);
}
#inbox-list ion-item {
  transition: opacity 0.3s ease-in-out, color 0.3s ease-in-out;
  cursor: pointer;
}
#inbox-list ion-item:hover {
  color: var(--ion-color-primary);
}
</style>