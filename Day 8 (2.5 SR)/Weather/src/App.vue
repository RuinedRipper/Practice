<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-100">
    <div class="bg-white rounded-lg p-8 shadow-md text-center">
      <router-link
        to="/"
        class="text-2xl font-bold text-blue-600 hover:text-blue-800 transition duration-300 ease-in-out"
      >
        Погода
      </router-link>
      <router-view class="mt-4"></router-view>
    </div>
  </div>
</template>
