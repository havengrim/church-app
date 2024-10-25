<template>
  <ion-page>
    <!-- <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Blank</ion-title>
      </ion-toolbar>
    </ion-header> -->

    <ion-content :fullscreen="true">
      <!-- <div class="webview-container">
        <iframe :src="link" width="100%" height="100%" frameborder="0"></iframe>
      </div> -->
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, onIonViewWillEnter } from '@ionic/vue';
import { ref } from 'vue';
import { InAppBrowser } from '@awesome-cordova-plugins/in-app-browser';
import { App } from '@capacitor/app';
const link = ref("https://clhcc.vercel.app/");

onIonViewWillEnter(async () => {
  const options = 'location=no,toolbar=no,hidden=no, hidenavigationbuttons=yes, zoom=no, presentationstyle=fullscreen, hideurlbar=yes, pullToRefresh=no';
  let browser : any;
  try {
    browser = InAppBrowser.create(link.value, '_self', options);
    browser.on('loadstop').subscribe(() => {
      // browser.executeScript({
      //   code: `
      //     if (navigator.userAgent.includes('Android')) {
      //       var meta = document.createElement('meta');
      //       meta.name = 'viewport';
      //       meta.content = 'width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no';
      //       document.getElementsByTagName('head')[0].appendChild(meta);
          
      //       document.querySelector('html').style.zoom = '1';  // Ensure no zoom scaling
      //     }

      //    let touchStartY = 0;
      //     let touchMoveY = 0;
      //     let isPulling = false;
      //     const pullThreshold = 80; // Threshold to trigger refresh
      //     const refreshIndicator = document.createElement('div');

      //     // Create refresh indicator element
      //     refreshIndicator.style.position = 'fixed';
      //     refreshIndicator.style.top = '0';
      //     refreshIndicator.style.left = '0';
      //     refreshIndicator.style.width = '100%';
      //     refreshIndicator.style.height = '50px';
      //     refreshIndicator.style.backgroundColor = '#2196F3';
      //     refreshIndicator.style.color = 'white';
      //     refreshIndicator.style.textAlign = 'center';
      //     refreshIndicator.style.lineHeight = '50px';
      //     refreshIndicator.style.display = 'none';
      //     refreshIndicator.innerHTML = 'Pull down to refresh';
          
      //     // Append the refresh indicator to the header (or body if no header exists)
      //     const header = document.querySelector('header') || document.body;
      //     header.prepend(refreshIndicator); // Add refresh indicator to the top

      //     // Detect touchstart event (when user touches the screen)
      //     document.addEventListener('touchstart', function(e) {
      //       if (window.scrollY === 0) {
      //         touchStartY = e.touches[0].screenY;
      //         isPulling = true;
      //       }
      //     });

      //     // Detect touchmove event (when user pulls down)
      //     document.addEventListener('touchmove', function(e) {
      //       if (isPulling && window.scrollY === 0) {
      //         touchMoveY = e.touches[0].screenY;

      //         // Calculate how far the user has pulled down
      //         let pullDistance = touchMoveY - touchStartY;

      //         if (pullDistance > 0) {
      //           e.preventDefault(); // Prevent native scroll
      //           refreshIndicator.style.display = 'block';
      //           refreshIndicator.innerHTML = 'Release to Refresh';
      //         }
      //       }
      //     });

      //     // Detect touchend event (when user lifts their finger)
      //     document.addEventListener('touchend', function() {
      //       if (isPulling && window.scrollY === 0) {
      //         let pullDistance = touchMoveY - touchStartY;

      //         if (pullDistance > pullThreshold) {
      //           // Trigger page refresh if pull exceeds threshold
      //           refreshIndicator.innerHTML = 'Refreshing...';
      //           window.location.reload();
      //         } else {
      //           // Hide refresh indicator if pull not sufficient
      //           refreshIndicator.style.display = 'none';
      //         }
      //       }
      //       isPulling = false;
      //     });
      //   `
      // });

      browser.executeScript({
        code : `
          if (navigator.userAgent.includes('Android')) {
             var meta = document.createElement('meta');
             meta.name = 'viewport';
             meta.content = 'width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no';
             document.getElementsByTagName('head')[0].appendChild(meta);
          
             document.querySelector('html').style.zoom = '1';  // Ensure no zoom scaling
          }

          if (navigator.userAgent.includes('Android')) {
             var meta = document.createElement('meta');
             meta.name = 'viewport';
             meta.content = 'width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no';
             document.getElementsByTagName('head')[0].appendChild(meta);
          
             document.querySelector('html').style.zoom = '1';  // Ensure no zoom scaling
          }

          // Remove any existing refresh indicator to avoid duplicates
          const existingIndicator = document.querySelector('.custom-refresh-indicator');
          if (existingIndicator) {
            existingIndicator.remove();
          }

          // Create a new refresh indicator at the top of the page
          const refreshIndicator = document.createElement('div');
          refreshIndicator.classList.add('custom-refresh-indicator');
          refreshIndicator.style.position = 'fixed';
          refreshIndicator.style.top = '0'; // Fix to the top of the viewport
          refreshIndicator.style.left = '0';
          refreshIndicator.style.width = '100%';
          refreshIndicator.style.height = '50px';
          refreshIndicator.style.backgroundColor = '#2196F3';
          refreshIndicator.style.color = 'white';
          refreshIndicator.style.textAlign = 'center';
          refreshIndicator.style.lineHeight = '50px';
          refreshIndicator.style.zIndex = '1000';
          refreshIndicator.style.display = 'none'; // Hidden by default
          refreshIndicator.innerHTML = 'Pull down to refresh';

          // Append the refresh indicator to the body (at the top)
          document.body.appendChild(refreshIndicator);

          let touchStartY = 0;
          let touchMoveY = 0;
          let isPulling = false;
          let holdTimer = null;
          const pullThreshold = 80; // Threshold to trigger refresh
          const holdDuration = 1000; // 1 seconds hold

          // Detect touchstart event (when user touches the screen)
          document.addEventListener('touchstart', function(e) {
            // Only trigger when at the very top of the page
            if (window.scrollY === 0) {
              touchStartY = e.touches[0].screenY;
              isPulling = true;

              // Start a timer for the 2-second hold
              holdTimer = setTimeout(() => {
                if (isPulling) {
                  // After 2 seconds, if still pulling, show the refresh indicator
                  refreshIndicator.style.display = 'block';
                  refreshIndicator.innerHTML = 'Release to refresh';
                }
              }, holdDuration);
            }
          });

          // Detect touchmove event (when user pulls down)
          document.addEventListener('touchmove', function(e) {
            if (isPulling && window.scrollY === 0) {
              touchMoveY = e.touches[0].screenY;

              // Calculate the pull distance (downward pull)
              let pullDistance = touchMoveY - touchStartY;

              if (pullDistance > 0) {
                e.preventDefault(); // Prevent native scroll
              }
            }
          });

          // Detect touchend event (when user releases the pull)
          document.addEventListener('touchend', function() {
            if (isPulling && window.scrollY === 0) {
              let pullDistance = touchMoveY - touchStartY;

              // If the pull is more than the threshold and the indicator is visible
              if (pullDistance > pullThreshold && refreshIndicator.style.display === 'block') {
                refreshIndicator.innerHTML = 'Refreshing...';
                window.location.reload(); // Trigger refresh
              } else {
                // If not enough pull or timer didn't trigger, hide the indicator
                refreshIndicator.style.display = 'none';
              }
            }

            // Clear the timer and reset pulling state
            clearTimeout(holdTimer);
            isPulling = false;
          });
        `
      })
    });

    browser.on('exit').subscribe(() => {
      console.log('Browser closed');
      App.exitApp();
    });
  } catch(error) {
    console.log(error);
  }
});

</script>

<style scoped>
.webview-container {
  height: 100%;
  width: 100%;
}
iframe {
  height: 100%;
  width: 100%;
}
</style>