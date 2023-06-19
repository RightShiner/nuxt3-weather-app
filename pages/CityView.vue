<template>
  <div>
    <ClientOnly>
      <Suspense>
        <AsyncCityView />
        <template #fallback>
          <Loading />
        </template>
      </Suspense>
    </ClientOnly>
  </div>
</template>

<script setup>
import AsyncCityView from '~~/components/AsyncCityView.vue'

</script>

<style></style>