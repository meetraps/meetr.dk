<template>
  <BaseSection id="kontakt" class="overflow-hidden" :bg-color="'gray-900'">
    <h3 class="responsive-text-base pb-6 text-white">KONTAKT OS</h3>
    <TextFader
      :uid="'contact-intro'"
      :text="pageIntro"
      :highlight-first-words="4"
      :text-color="'white'"
      :highlight-color="'logo1'"
      class="mb-12"
    />
    <BaseFadeIn :uid="'876'">
      <dl class="my-12 space-y-6">
        <dt><span class="sr-only">Email</span></dt>
        <Transition
          :css="false"
          mode="out-in"
          @enter="enterLeftElement"
        >
          <dd v-if="showSections" class="flex items-center text-indigo-50">
            <MailIcon class="flex-shrink-0 w-12 h-12 sm:w-16 sm:h-16 text-logo1" aria-hidden="true" />
            <span class="ml-3 sm:ml-6 pb-2 responsive-text-mega"><a href="mailto:mads@nodalit.dk">{{ contactEmail }}</a></span>
          </dd>
        </Transition>
      </dl>
    </BaseFadeIn>
    <BaseFadeIn :uid="'877'">
      <div class="relative bg-white shadow-xl">
        <h2 class="sr-only">Kontakt os</h2>
        <div class="">
          <div class="py-10 px-6 sm:px-10 lg:col-span-2 xl:p-12">
            <h3 class="text-lg font-bold text-gray-900 uppercase">SEND EN BESKED</h3>
            <div class="mt-6 grid grid-cols-1 gap-y-6 sm:grid-cols-2 sm:gap-x-8">
              <div>
                <label for="name" class="block text-sm font-medium text-gray-900">Navn</label>
                <div class="mt-1">
                  <input
                    id="name"
                    v-model="name"
                    type="text"
                    name="name"
                    autocomplete="name"
                    class="py-3 px-4 block w-full shadow-sm text-gray-900 focus:ring-indigo-500 focus:border-indigo-500 border-gray-300 rounded-md"
                  />
                </div>
              </div>
              <div>
                <label for="email" class="block text-sm font-medium text-gray-900">Email</label>
                <div class="mt-1">
                  <input
                    id="email"
                    v-model="email"
                    name="email"
                    type="email"
                    autocomplete="email"
                    class="py-3 px-4 block w-full shadow-sm text-gray-900 focus:ring-indigo-500 focus:border-indigo-500 border-gray-300 rounded-md"
                  />
                </div>
              </div>
              <div class="sm:col-span-2">
                <div class="flex justify-between">
                  <label for="message" class="block text-sm font-medium text-gray-900">Besked</label>
                  <span id="message-max" class="text-sm text-gray-500">Max. 500 tegn</span>
                </div>
                <div class="mt-1">
                  <textarea
                    id="message"
                    v-model="message"
                    name="message"
                    rows="4"
                    class="py-3 px-4 block w-full shadow-sm text-gray-900 focus:ring-indigo-500 focus:border-indigo-500 border border-gray-300 rounded-md"
                    aria-describedby="message-max"
                  />
                </div>
              </div>
            </div>
            <div>
              <div class="h-12">
                <div>
                  <Transition name="slide-fade">
                    <div v-if="isSent" class="sm:col-span-2 sm:flex">
                      <p class="text-center text-gray-900 font-medium text-xl my-2">Din besked er sendt. Tak for din henvendelse.</p>
                    </div>
                  </Transition>
                  <Transition name="slide-fade">
                    <div v-if="isError" class="sm:col-span-2 sm:flex">
                      <p class="text-center text-red-700 font-medium text-xl my-2">Der opstod en fejl – prøv meget gerne igen.</p>
                    </div>
                  </Transition>
                </div>
              </div>
              <Transition
                :css="false"
                mode="out-in"
                @enter="enterRightElement"
              >
                <div v-if="showSections" class="sm:col-span-2 sm:flex sm:justify-end">
                  <button type="submit" class="mt-2 w-full inline-flex items-center justify-center px-6 py-3 border border-transparent rounded-md shadow-sm text-base font-bold text-black bg-logo1 hover:bg-pink-600 hover:text-white focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-black sm:w-auto" :disabled="isSending" @click="send">
                    <svg v-if="isSending" class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                      <circle
                        class="opacity-25"
                        cx="12"
                        cy="12"
                        r="10"
                        stroke="currentColor"
                        stroke-width="4"
                      ></circle>
                      <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                    </svg>
                    <span v-if="isSending">Sender...</span>
                    <span v-if="!isSending">SEND</span>
                  </button>
                </div>
              </Transition>
            </div>
          </div>
        </div>
      </div>
    </BaseFadeIn>
  </BaseSection>
</template>

<script setup lang="ts">
import { MailIcon } from '@heroicons/vue/outline/esm/index.js'
import anime from 'animejs/lib/anime.es.js'
import texts from '@/assets/texts'
const pageIntro = texts.contact.intro
const contactEmail = texts.contact.email
const email = ref('')
const name = ref('')
const message = ref('')
const isSending = ref(false)
const isSent = ref(false)
const isError = ref(false)
const showSections = ref(false)

onMounted(() => {
  showSections.value = true
})

const send = async (e: Event) => {
  e.preventDefault()
  isSending.value = true
  const response = await fetch('/api/contact', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      name: name.value,
      email: email.value,
      message: message.value,
    }),
  })
  const json = await response.json()
  isSending.value = false
  if (json.success) {
    isSent.value = true
    email.value = ''
    name.value = ''
    message.value = ''
    setTimeout(() => {
      isSent.value = false
    }, 5000)
  } else {
    isError.value = true
    setTimeout(() => {
      isError.value = false
    }, 5000)
  }
}

const enterLeftElement = (el: HTMLElement, done) => {
  anime({
    targets: el,
    translateX: [-400, 0],
    // easing: 'easeOutExpo',
    complete: done,
    duration: 750,
  })
}

const enterRightElement = (el: HTMLElement, done) => {
  anime({
    targets: el,
    translateX: [400, 0],
    easing: 'easeOutExpo',
    complete: done,
    duration: 750,
  })
}
</script>

<style>
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}

.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateY(-20px);
  opacity: 0;
}
</style>
