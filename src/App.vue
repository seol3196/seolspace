<template>
  <div id="app">
    <Index />
  </div>
</template>

<script>
import Index from '@/components/Index.vue';
import KoreanSimilarityAnalyzer from './components/KoreanSimilarityAnalyzer.vue'

export default {
  name: 'App',
  components: {
    Index // Index 컴포넌트를 등록
  }
};
</script>
<style>
/* App.vue에 대한 스타일이 필요하다면 여기에 추가하세요 */
</style>

