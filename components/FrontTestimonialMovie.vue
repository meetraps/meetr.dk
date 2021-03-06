<template>
  <div ref="movieContainerRef">
    <div class="base-section pb-16 bg-gray-200 lg:pb-0 lg:z-10 lg:relative">
      <div class="md:grid md:grid-cols-12 lg:mx-auto lg:max-w-screen-2xl lg:px-8 lg:gap-8">
        <div class="sm:max-w-2xl md:hidden px-12 pt-12">
          <h3 class="responsive-text-base uppercase">Reference</h3>
          <h4 class="text-5xl lg:text-6xl">LINK Arkitektur</h4>
        </div>
        <div class="relative flex flex-col items-center md:block md:col-span-4 xl:col-span-3">
          <div aria-hidden="true" class="absolute inset-x-0 top-0 h-1/2 bg-white hidden" />
          <div class="rounded-2xl relative">
            <div>
              <video ref="vidRef" playsinline muted preload="metadata">
                <source src="/mobile.mp4" type="video/mp4" codec="hvc1" />
                <source src="/mobile.webm" type="video/webm" />
              </video>
            </div>
          </div>
        </div>
        <div class="lg:m-0 md:col-span-8 xl:col-span-8 md:pl-9">
          <div class="sm:max-w-2xl lg:max-w-none px-12 lg:px-0 lg:py-12">
            <blockquote>
              <h3 class="hidden md:block responsive-text-base uppercase">Reference</h3>
              <div>
                <h3 class="hidden md:block text-5xl lg:text-6xl">LINK Arkitektur</h3>
                <!-- <img src="/assets/link_arkitektur_logo.svg" class="h-24 lg:h-12" /> -->
                <p class="mt-6 responsive-text-lg text-black">"{{ testimonial.quote }}"</p>
              </div>
              <footer class="mt-6 md:flex">
                <div class="md:flex md:items-center md:justify-center">
                  <div class="md:flex-shrink-0">
                    <img class="mx-auto h-10 w-10 rounded-full" :src="testimonial.image" alt="" />
                  </div>
                  <div class="mt-3 text-center md:mt-0 md:ml-4 md:flex md:items-center">
                    <div class="text-base font-medium text-gray-900">{{ testimonial.name }}</div>

                    <svg class="hidden md:block mx-1 h-5 w-5 text-logo3" fill="currentColor" viewBox="0 0 20 20">
                      <path d="M11 0h3L9 20H6l5-20z" />
                    </svg>

                    <div class="text-base font-medium text-gray-500">{{ testimonial.title }}, {{ testimonial.company }}</div>
                  </div>
                </div>
              </footer>
            </blockquote>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import texts from '@/assets/texts'
import calcHorizontalDistance from '@/composables/calcHorizontalDistance'

const vidRef = ref(null)
const movieContainerRef = ref(null)
let requestAnimationFrameId: number
let currentFrame = 1

const tick = () => {
  if (process.client) {
    requestAnimationFrameId = requestAnimationFrame(tick)
    if (vidRef.value.seeking) {
      return
    }
    const horDist = calcHorizontalDistance(65, movieContainerRef.value)
    const maxScroll = movieContainerRef.value.clientHeight - horDist
    if (horDist > maxScroll) {
      if (currentFrame < 30) {
        currentFrame = 30
        vidRef.value.currentTime = 1
      }
      return
    }
    const scrollProgress = horDist / Math.max(maxScroll, 1)
    const newPos = Math.round(vidRef.value.duration * scrollProgress * 100) / 100
    const newFrame = Math.round(newPos * 30)
    if (newFrame !== currentFrame) {
      if (newFrame > 30) {
        vidRef.value.currentTime = 1
        return
      }
      vidRef.value.currentTime = isNaN(newPos) ? 0.001 : newPos
      currentFrame = newFrame
      // console.log(currentFrame, newPos)
    }
  }
}

onMounted(() => {
  if (process.client) {
    vidRef.value.currentTime = 0.001
    tick()
  }
})

onUnmounted(() => {
  if (process.client) {
    cancelAnimationFrame(requestAnimationFrameId)
  }
})

const testimonial = texts.testimonials[0]
</script>
