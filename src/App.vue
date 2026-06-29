<template>
  <div class="app-wrapper">
    <a href="#main-content" class="skip-link">跳到主要内容</a>

    <section class="www_vvhan_com" aria-label="今日热榜">
      <header role="banner">
        <div class="header-inner">
          <div class="logo">
            <svg class="logo-line-icon" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#333" stroke-width="1.5" aria-hidden="true">
              <rect x="3" y="3" width="18" height="18" rx="2" />
              <path d="M3 9h18" />
              <path d="M9 3v18" />
            </svg>
            <span>全网热门资讯与实时热榜</span>
          </div>
          <h2>提供各站热榜热搜聚合</h2>
          <button class="theme-toggle" @click="toggleTheme"
            :title="isDark ? '切换到亮色模式' : '切换到暗黑模式'"
            :aria-label="isDark ? '切换到亮色模式' : '切换到暗黑模式'">
            <SunIcon v-if="isDark" aria-hidden="true" />
            <MoonIcon v-else aria-hidden="true" />
          </button>
        </div>
      </header>

      <main id="main-content" role="main">
        <!-- 公告板块：描边风格 -->
        <aside class="announcement-section" aria-label="站点公告">
          <div class="announcement-card">
            <div class="announcement-header">
              <DrawingPinIcon aria-hidden="true" class="announcement-icon" />
              <h3 class="announcement-title">Welcome to Kaixin Hub</h3>
              <a class="announcement-link" href="https://axoxe.com/" target="_blank" rel="noopener noreferrer">
                多云禁止悲观丨KaiXin
              </a>
            </div>
            <div class="announcement-body">
              <p>
                今日热榜是聚合热榜热搜平台，汇集了各大网站的热榜信息，包括微博热搜、今日头条、知乎日报、澎湃新闻、虎扑步行街、36氪、哔哩哔哩热榜，知乎、IT资讯、虎嗅网、人人都是产品经理、百度、抖音热点豆瓣小组精选等。
              </p>
            </div>
          </div>
        </aside>

        <section class="hotlist" aria-label="热榜列表">
          <ListItem v-for="i in hotlistKey" :key="i.name" :item="i" @refreshFn="refreshFn" />
        </section>
      </main>

      <footer role="contentinfo">
        <p><img src="./assets/svg/ing.svg" alt="" /></p>
        <p>
          <a href="https://pages.cloudflare.com" target="_blank" rel="noopener noreferrer"><img src="./assets/svg/framework.svg" alt="Cloudflare Pages" /></a>
          <a href="https://www.cloudflare.com/zh-cn/application-services/products/cdn/" target="_blank" rel="noopener noreferrer"><img src="./assets/svg/cdn.svg" alt="Cloudflare CDN" /></a>
          <a href="https://vuejs.org" target="_blank" rel="noopener noreferrer"><img src="./assets/svg/web.svg" alt="Vue.js" /></a>
          <a href="https://api.vvhan.com" target="_blank"><img src="./assets/svg/surppot.svg" alt="API 支持" /></a>
        </p>
      </footer>
    </section>
    <Toaster />
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted } from 'vue'
import moment from 'moment'
import { DrawingPinIcon, SunIcon, MoonIcon } from '@radix-icons/vue'
import ListItem from '@/components/ListItem/ListItem.vue'
import { Toaster } from '@/components/ui/toast'
import { useToast } from '@/components/ui/toast/use-toast'
const { toast } = useToast()

// 暗黑模式
const isDark = ref(false)

const initTheme = () => {
  const stored = localStorage.getItem('theme')
  if (stored) {
    isDark.value = stored === 'dark'
  } else {
    isDark.value = window.matchMedia('(prefers-color-scheme: dark)').matches
  }
}
const toggleTheme = () => {
  isDark.value = !isDark.value
  localStorage.setItem('theme', isDark.value ? 'dark' : 'light')
}
watch(isDark, (val) => {
  document.documentElement.classList.toggle('dark', val)
}, { immediate: true })
onMounted(() => {
  initTheme()
  window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', (e) => {
    if (!localStorage.getItem('theme')) isDark.value = e.matches
  })
})

// 热榜列表
const hotlistKey = ref<any[]>([
  { key: 'toutiao', name: '今日头条', sub: '热点', data: [] },
  { key: 'pengPai', name: '澎湃新闻', sub: '时事', data: [] },
  { key: 'qqNews', name: '腾讯新闻', sub: '热点榜', data: [] },
  { key: 'wyNews', name: '网易新闻', sub: '热点榜', data: [] },
  { key: 'baiduRD', name: '百度热点', sub: '指数', data: [] },
  { key: 'wbHot', name: '微博', sub: '热搜榜', data: [] },
  { key: 'douyinHot', name: '抖音', sub: '热点榜', data: [] },
  { key: 'zhihuHot', name: '知乎热榜', sub: '热度', data: [] },
  { key: 'wbNews', name: '微博', sub: '要闻', data: [] },
  { key: 'huXiu', name: '虎嗅', sub: '最新资讯', data: [] },
  { key: 'gcores', name: '机核', sub: '资讯', data: [] },
  { key: 'zhihuDay', name: '知乎日报', sub: '', data: [] },
  { key: '36Ke', name: '36氪', sub: '24小时热榜', data: [] },
  { key: 'itNews', name: 'IT之家', sub: '最新资讯', data: [] },
  { key: 'chongBluo', name: '虫部落', sub: '最新热门', data: [] },
  { key: 'woShiPm', name: 'woShiPm', sub: '热榜', data: [] }
])

// 初始化数据请求
const vhInit = async () => {
  try {
    const res = await fetch('https://hot-api.vhan.eu.org/v2?type=all')
    await new Promise((r) => setTimeout(r, 666))
    toast({ description: '🚀 全网热榜加载完成' })
    const { data } = await res.json()
    hotlistKey.value.forEach((i: any) => {
      const currentItem = data.find((item: any) => item.name == i.name && item.subtitle == i.sub)
      if (currentItem) {
        i.data = currentItem.data
        i.updateStr = formatTime(currentItem.update_time)
      }
    })
  } catch (error) {
    toast({ description: '今日热榜 获取失败', variant: 'destructive' })
  }
}

// 刷新数据
const updateStatus = ref<boolean>(false)
const refreshFn = async (item: any) => {
  if (updateStatus.value) return
  updateStatus.value = true
  item.data = []
  const res = await fetch(`https://hot-api.vhan.eu.org/v2?type=${item.key}`)
  const data = await res.json()
  await new Promise((r) => setTimeout(r, 666))
  item.data = data.data
  item.updateStr = formatTime(data.update_time)
  toast({ title: 'Update', description: `${item.name} 更新成功` })
  updateStatus.value = false
}
vhInit()

// 时间处理
const formatTime = (time: string) => {
  const targetDateTime = moment(time)
  const now = moment()
  const duration = moment.duration(now.diff(targetDateTime))
  return `${duration.hours()}小时${duration.minutes()}分钟前`
}
</script>

<style scoped>
@import '@/assets/index.less';
</style>
