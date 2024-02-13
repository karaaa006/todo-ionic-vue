<template>
  <ion-app>
    <div>
      <Suspense>
        <ion-router-outlet />
      </Suspense>
    </div>
  </ion-app>
</template>

<script setup lang="ts">
import { IonApp, IonRouterOutlet } from "@ionic/vue";
</script>

<style lang="scss" scoped></style>
