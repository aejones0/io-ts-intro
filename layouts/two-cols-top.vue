<!--
  Usage:

```md
---
layout: two-cols-top
---

::top::

# Top

This shows above the columns

::left::

# Left

This shows on the left

::right::

# Right

This shows on the right
```

-->

<script setup lang="ts">
const props = defineProps({
  class: {
    type: String,
  },
})
</script>

<template>
  <div class="slidev-layout h-full w-full ">
    <div class="center w-full ">
        <slot name="top" />
    </div>
    <div class="two-columns w-full grid grid-cols-2 gap-x-3">
      <div class="col-left" :class="props.class">
        <slot name="left" />
      </div>
      <div class="col-right" :class="props.class">
        <slot name="right" />
      </div>
    </div>
  </div>
</template>
