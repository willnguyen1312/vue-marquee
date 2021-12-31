<script setup lang="ts">
import { ref, computed } from "vue";
import { useRafFn, useToggle } from "@vueuse/core";
import { nanoid } from "nanoid";

const translateX = ref(0);
const direction = ref<"left" | "right">("right");

const list = ref<HTMLDivElement>();
const [isHover, toggleIsHover] = useToggle(false);

const pixelsMovedPerFrameOnIdle = 1.5;
const pixelsMovedPerFrameOnHover = 0.5;

useRafFn(() => {
  const bound = list.value?.clientWidth;

  if (bound) {
    const pixelsMovedPerFrame = isHover.value
      ? pixelsMovedPerFrameOnHover
      : pixelsMovedPerFrameOnIdle;
    const newTranslateX = translateX.value + pixelsMovedPerFrame;

    if (newTranslateX > bound) {
      translateX.value = 0;
    } else {
      translateX.value = newTranslateX;
    }
  }
});

const style = computed(() => {
  const bound = list.value?.clientWidth || 0;
  const newValue =
    direction.value === "left" ? -translateX.value : translateX.value - bound;
  return {
    transform: `translateX(${newValue}px)`,
  };
});

const images: { src: string; id: string }[] = Array.from(
  { length: 8 },
  (_, index) => ({ src: `/${index + 1}.jpeg`, id: nanoid() })
);

const handlePointerEnter = () => {
  toggleIsHover();
};

const handlePointerLeave = () => {
  toggleIsHover();
};
</script>

<template>
  <div class="m-auto flex flex-col items-center px-4">
    <h1 class="my-10 text-4xl font-bold">Demo Marquee</h1>
    <div
      @pointerenter="handlePointerEnter"
      @pointerleave="handlePointerLeave"
      class="overflow-hidden w-full md:w-8/12 cursor-pointer"
    >
      <div class="flex will-change-transform" :style="style">
        <div ref="list" class="flex flex-shrink-0 py-4 relative">
          <img
            v-for="image in images"
            :key="image.id"
            :src="image.src"
            class="h-40 w-40 mx-2 hover:ring-4 hover:ring-pink-700"
          />
        </div>

        <!-- Clone -->
        <div class="flex flex-shrink-0 py-4 relative">
          <img
            v-for="image in images"
            :key="image.id"
            :src="image.src"
            class="h-40 w-40 mx-2 hover:ring-4 hover:ring-pink-700"
          />
        </div>
      </div>
    </div>
  </div>
</template>
