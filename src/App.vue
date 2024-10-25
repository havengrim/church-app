<template>
  <ion-app>
      <ion-router-outlet />
  </ion-app>
</template>

<script setup lang="ts">
import { IonApp, IonRouterOutlet, useBackButton, useIonRouter } from '@ionic/vue';
import { App } from '@capacitor/app';
import { StatusBar, Style } from '@capacitor/status-bar';
import { isPlatform } from '@ionic/vue';

const ionRouter = useIonRouter();

// Exit the application if there is nothing left in the navigation stack
useBackButton(-1, () => {
  if (!ionRouter.canGoBack()) {
      App.exitApp();
  }
});

async () => {
  try {
      if (isPlatform('mobile')) {
          await StatusBar.setBackgroundColor({ color: '#ffffff' });
          await StatusBar.setStyle({ style: Style.Light });
      }
  } catch (error) {
      console.error('Failed to change status bar color:', error);
  }
}
</script>
