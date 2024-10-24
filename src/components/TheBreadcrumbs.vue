<template>
  <v-container fluid class="pa-0">
    <v-row d-flex>
      <v-col>
        <div
          class="d-flex justify-space-between align-center flex-wrap ga-4 flex-fill"
        >
          <slot name="prepend"></slot>
          <slot name="title">
            <h1 class="text-h5 text-md-h4 ps-1 align-start">{{ title }}</h1>
          </slot>

          <strong
            class="me-2 text-medium-emphasis text-body-2 d-inline-flex align-center flex-fill"
          >
            <slot name="center"></slot>
          </strong>

          <div class="d-flex ga-2 align-end">
            <slot name="append"> </slot>
          </div>
        </div>
      </v-col>
    </v-row>

    <v-breadcrumbs
      v-if="breadcrumbs.length > 0"
      class="px-0 pb-2 text-body-2"
      :items="breadcrumbs"
    >
      <template #prepend>
        <v-icon icon="mdi-view-dashboard" size="small"></v-icon>
      </template>
    </v-breadcrumbs>
  </v-container>
</template>

<script setup lang="ts">
import { computed } from "vue";
import { useRoute } from "vue-router";

defineProps({
  title: {
    type: String,
    default: undefined,
  },
});

const route = useRoute();

const breadcrumbs = computed(() => {
  const namesToExclude = [];

  // Filter matched routes, excluding unwanted ones
  let matchedRoutes: RouteLocationMatched[] = route.matched.filter(
    (r) => !namesToExclude.includes(r.name as string)
  );

  // Remove duplicates by path
  matchedRoutes = [
    ...new Map(matchedRoutes.map((item) => [item.path, item])).values(),
  ];

  return matchedRoutes.map((r, index) => {
    const isLast = index === matchedRoutes.length - 1;

    // Collect only the parameters that this route requires from the current route
    // eslint-disable-next-line  @typescript-eslint/no-explicit-any
    const requiredParams: Record<string, any> = {};
    for (const paramKey in route.params) {
      // Only add the param if this routeRecord has a dynamic segment matching it
      if (r.path.includes(`:${paramKey}`)) {
        requiredParams[paramKey] = route.params[paramKey];
      }
    }

    // Build a `to` link for named routes or manually construct a path for unnamed routes
    // eslint-disable-next-line  @typescript-eslint/no-explicit-any
    let to: string | Record<string, any> | null = null;

    if (!isLast) {
      if (r.name) {
        // Use route name and params if available
        to = { name: r.name as string, params: requiredParams };
      } else if (r.path) {
        // Manually replace the path params if no name is provided
        to = r.path.replace(/:(\w+)/g, (_, key) => requiredParams[key] || "");
      }
    }

    // Use the title from meta, and fall back to the route name
    const title = r.meta?.title ? t(r.meta.title as string) : r.name;

    // Return the breadcrumb object
    return {
      title,
      to,
    };
  });
});

console.warn("route", route)
</script>
