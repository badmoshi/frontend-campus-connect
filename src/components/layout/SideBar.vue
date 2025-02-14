<template>
  <aside
    class="fixed top-0 left-0 z-40 bg-white w-64 h-screen mt-3 transition-transform -translate-x-full lg:translate-x-0"
    aria-label="Sidenav"
    id="drawer-navigation"
  >
    <div class="flex flex-col justify-center px-3 h-full dark:bg-gray-800">
      <div class="w-full h-22 flex items-start justify-center mb-6">
        <div @click="goToProfile(User?.user_id)" class="flex flex-col items-center cursor-pointer">
          <div>
            <img :src="User?.attributes.avatar" class="rounded-full w-14 h-14" />
          </div>
          <div class="flex flex-col items-center">
            <span class="text-md">{{ User?.attributes.name }}</span>
            <span class="text-sm">{{ User?.attributes.email }}</span>
          </div>
        </div>
      </div>
      <ul class="space-y-2 ml-3 mr-3">
        <li>
          <a
            href="/"
            class="flex items-center p-2 text-base font-medium text-white-900 rounded-lg dark:text-white hover:bg-primary hover:text-white dark:hover:bg-gray-700 group"
          >
            <span class="transform transition-transform group-hover:scale-110">
              <IconHome class="group-hover:text-white" />
            </span>
            <span class="ml-3">Home</span>
          </a>
        </li>

        <li>
          <a
            :href="`/profile/${User?.user_id}`"
            class="flex items-center p-2 text-base font-medium text-white-900 rounded-lg dark:text-white hover:bg-primary hover:text-white dark:hover:bg-gray-700 group"
          >
            <span class="transform transition-transform group-hover:scale-110">
              <IconProfile class="group-hover:text-white" />
            </span>
            <span class="ml-3">Your Profile</span>
          </a>
        </li>

        <Sheet class="h-full w-full">
          <li>
            <SheetTrigger class="flex items-center justify-center w-full">
              <a
                href="#"
                class="w-full flex items-center p-2 text-base font-medium text-white-900 rounded-lg dark:text-white hover:bg-primary hover:text-white dark:hover:bg-gray-700 group"
              >
                <span class="transform transition-transform group-hover:scale-110">
                  <IconBell class="group-hover:text-white" />
                </span>
                <span class="ml-3">Notifications</span>
                <div
                  v-if="notificationCount && notificationCount !== 0"
                  class="ml-8 text-[12px] rounded-lg bg-red-500 text-white w-6 h-5 flex items-center justify-center"
                >
                  {{ notificationCount }}
                </div>
              </a>
            </SheetTrigger>
          </li>
          <SheetContent class="w-[400px] sm:w-[540px]">
            <SheetHeader>
              <SheetTitle class="font-semibold text-xl">Notifications</SheetTitle>
              <hr class="mt-3 w-full h-[1px] rounded-xl bg-gray-300" />
            </SheetHeader>
            <div class="flex flex-col items-center justify-center">
              <Notification :notifications="notifications" @clear="clearNotifications" />
            </div>
          </SheetContent>
        </Sheet>

        <li>
          <a
            href="/setting"
            class="flex items-center p-2 text-base font-medium text-white-900 rounded-lg dark:text-white hover:bg-primary hover:text-white dark:hover:bg-gray-700 group"
          >
            <span class="transform transition-transform group-hover:scale-110">
              <IconSetting class="group-hover:text-white" />
            </span>
            <span class="ml-3">Settings</span>
          </a>
        </li>

        <li @click="logout">
          <a
            href="#"
            class="flex items-center p-2 text-base font-medium text-white-900 rounded-lg dark:text-white hover:bg-primary hover:text-white dark:hover:bg-gray-700 group"
          >
            <span class="transform transition-transform group-hover:scale-110">
              <IconExit class="group-hover:text-white" />
            </span>
            <span class="ml-3">Logout</span>
          </a>
        </li>
      </ul>
      <div class="mt-2 w-full h-32">
        <div class="flex flex-col items-center justify-center mt-5">
          <router-link to="/">
            <!-- <img src="@/assets/logoo.png" alt="" class="h-40" /> -->
            <!-- <img src="@/assets/logoo.png" alt="" /> -->

            <img src="@/assets/logo-campus-connect.png" alt="" class="h-14" />
            <img src="@/assets/logo with name.png" alt="" class="h-16" />

            <!-- <img src="@/assets/logo-campus-connect.png" alt="" class="h-12" /> -->
          </router-link>
          <!-- <img src="@/assets/campus connect logo.png" alt="" class="h-22 w-52" /> -->
          <!-- <span class="text-md font-semibold">Campus Connect</span> -->
        </div>
      </div>
    </div>
  </aside>
</template>

<script setup>
import { Sheet, SheetContent, SheetHeader, SheetTitle, SheetTrigger } from '@/components/ui/sheet'
import Notification from '@/components/Notifications/Notification.vue'
import { computed, ref, onMounted } from 'vue'
import { useStore } from 'vuex'
import router from '@/router'
import axios from '@/api.js'
import IconBell from '@/components/icons/IconBell.vue'
import IconHome from '@/components/icons/IconHome.vue'
import IconProfile from '@/components/icons/IconProfile.vue'
import IconExit from '@/components/icons/IconExit.vue'
import IconSetting from '@/components/icons/IconSetting.vue'

const store = useStore()

const User = computed(() => store.state.User.user)

console.log(User.value)

const logout = async () => {
  await store.dispatch('User/logout')
  router.push('/login')
}

const notifications = ref(null)
const notificationCount = ref(null)

const goToProfile = (userId) => {
  router.push({ name: 'profile-page-id', params: { id: userId } })
}

onMounted(async () => {
  try {
    const res = await axios.get(`/api/user/${User.value.user_id}/notification`)
    console.log(res)
    notifications.value = res.data.notifications
    notificationCount.value = notifications.value?.length
  } catch (error) {
    console.log(error)
  }
})

const clearNotifications = async () => {
  try {
    await axios.get(`/api/user/${User.value.user_id}/notification/read-all`)
    notifications.value = ''
    notificationCount.value = 0
  } catch (error) {
    console.log(error)
  }
}
</script>
