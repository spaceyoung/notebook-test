<template>
  <div class="loading w-100 h-100 mx-n4 my-n8 ma-n8">
    <div class="loading-box">
      <Vue3Lottie :animationData="LoadingAnimation" class="loading-lottie" loop autoplay />
      <span class="loading-text d-block">불러오는 중...</span>
    </div>
  </div>
</template>

<script setup>
import { Vue3Lottie } from 'vue3-lottie';
import LoadingAnimation from '../../assets/loading.json';
</script>

<style></style>