<template>
  <div v-if="images.length > 0" class="wrapper--dapp-images">
    <div class="row--images">
      <button class="button-arrow" @click="scrollLeft">
        <astar-icon-arrow-left-long class="button-arrow" :size="arrowSize" />
      </button>
      <div class="main-scroll-div">
        <div class="cover">
          <div class="scroll-images">
            <div v-for="(image, index) in images" :key="index" class="child">
              <img :src="image" alt="index" class="child-img" @click="handleOpenPicture(index)" />
            </div>
          </div>
        </div>
      </div>
      <div>
        <button class="button-arrow" @click="scrollRight">
          <astar-icon-arrow-right-long :size="arrowSize" />
        </button>
      </div>
    </div>

    <!-- Memo: fullscreen image preview -->
    <div>
      <q-carousel
        v-if="isFullScreen"
        v-model="slide"
        v-model:fullscreen="isFullScreen"
        swipeable
        animated
        arrows
        infinite
      >
        <q-carousel-slide
          v-for="(image, index) in images"
          :key="index"
          :name="index"
          :img-src="image"
        />

        <template #control>
          <q-carousel-control position="bottom-right" :offset="[18, 18]">
            <q-btn
              push
              round
              dense
              color="white"
              text-color="primary"
              :icon="isFullScreen ? 'fullscreen_exit' : 'fullscreen'"
              @click="isFullScreen = false"
            />
          </q-carousel-control>
        </template>
      </q-carousel>
    </div>
  </div>
</template>
<script lang="ts">
import { useBreakpoints } from 'src/hooks';
import { CombinedDappInfo } from 'src/staking-v3/logic';
import { computed, defineComponent, ref, watchEffect, onUnmounted, PropType } from 'vue';
export default defineComponent({
  props: {
    dapp: {
      type: Object as PropType<CombinedDappInfo>,
      required: true,
    },
  },
  setup(props) {
    const { width, screenSize } = useBreakpoints();
    const slide = ref<number>(0);
    const isFullScreen = ref<boolean>(false);

    const arrowSize = computed<number>(() => (width.value > screenSize.sm ? 40 : 30));
    const images = computed<string[]>(() => {
      try {
        if (props.dapp.extended !== undefined && props.dapp.extended.imagesUrl.length > 0) {
          return props.dapp.extended.imagesUrl;
        }

        return [];
      } catch (error) {
        return [];
      }
    });

    const scrollLeft = (): void => {
      const isSm = width.value > screenSize.sm;
      const move = isSm ? -350 : -250;
      const left = document.querySelector('.scroll-images');
      left && left.scrollBy(move, 0);
    };

    const scrollRight = (): void => {
      const isSm = width.value > screenSize.sm;
      const move = isSm ? 350 : 250;
      const right = document.querySelector('.scroll-images');
      right && right.scrollBy(move, 0);
    };

    const handleOpenPicture = (index: number): void => {
      isFullScreen.value = true;
      slide.value = index;
    };

    const handleKeyUp = ({ keyCode }: { keyCode: number }): void => {
      const escKey = 27;
      const leftKey = 37;
      const rightKey = 39;
      if (keyCode === escKey) {
        isFullScreen.value = false;
      } else if (keyCode === rightKey && slide.value !== images.value.length - 1) {
        slide.value = slide.value + 1;
      } else if (keyCode === leftKey && slide.value !== 0) {
        slide.value = slide.value - 1;
      }
      return;
    };

    const handleImageFullScreen = (): void => {
      if (isFullScreen.value) {
        document.addEventListener('keyup', handleKeyUp);
      }
    };

    watchEffect(handleImageFullScreen);
    onUnmounted(() => {
      document.removeEventListener('keyup', handleKeyUp);
    });

    return {
      slide,
      images,
      arrowSize,
      isFullScreen,
      scrollLeft,
      scrollRight,
      handleOpenPicture,
    };
  },
});
</script>

<style lang="scss" scoped>
@use './styles/dapp-images.scss';
</style>
