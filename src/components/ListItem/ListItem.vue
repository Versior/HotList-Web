<template>
  <section class="list-item" :aria-label="item.name + '热榜'">
    <header>
      <div class="l">
        <img :src="reqImg(item.name)" :alt="item.name + '图标'" />
        <h3>{{ item.name }}</h3>
      </div>
      <div class="r" aria-label="分类：{{ item.sub }}">{{ item.sub }}</div>
    </header>
    <main>
      <!-- WCAG: 使用有序列表呈现条目 -->
      <ol class="list" :class="{ active: item.data.length > 0 }">
        <li class="main-item" v-for="i in item.data" :key="i.index">
          <a :href="i.url" target="_blank" rel="noopener noreferrer">
            <span class="rank-badge" :class="'rank-' + i.index">{{ i.index }}</span>
            {{ i.title }}
          </a>
          <span class="hot-value" aria-label="热度 {{ i.hot }}">{{ i.hot }}</span>
        </li>
      </ol>
      <div class="loading-pn" :class="{ active: item.data.length < 1 }" aria-hidden="true">
        <div class="space-y-2">
          <Skeleton class="h-4 w-[100%]" />
          <Skeleton class="h-4 w-[80%]" />
          <Skeleton class="h-4 w-[90%]" />
          <Skeleton class="h-4 w-[60%]" />
          <Skeleton class="h-4 w-[70%]" />
          <Skeleton class="h-4 w-[50%]" />
          <Skeleton class="h-4 w-[30%]" />
          <Skeleton class="h-4 w-[40%]" />
        </div>
      </div>
    </main>
    <footer :class="{ active: item.data.length > 0 }">
      <span>{{ item.updateStr }}</span>
      <Button class="refresh" variant="outline" size="icon" @click="emit('refreshFn', item)"
        :aria-label="'刷新' + item.name + '热榜'">
        <UpdateIcon aria-hidden="true" />
      </Button>
    </footer>
  </section>
</template>

<script setup lang="ts">
import { ref, defineEmits } from 'vue';
import { UpdateIcon } from '@radix-icons/vue'
import { Button } from '@/components/ui/button'
import { Skeleton } from '@/components/ui/skeleton'
const emit = defineEmits(['refreshFn'])

const props = defineProps<{ item: any }>();
const item = ref<any>(props.item);
const reqImg = (src: string) => new URL(`../../assets/images/${src}.webp`, import.meta.url).href
</script>

<style scoped lang="less">
@import url('ListItem.less');
</style>