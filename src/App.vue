<template>
  <div class="app-wrapper">
    <a href="#main-content" class="skip-link">跳到主要内容</a>

    <!-- Memphis 装饰几何形状 -->
    <div class="memphis-shape shape-circle" style="top:120px;left:3%;width:60px;height:60px;" aria-hidden="true"></div>
    <div class="memphis-shape shape-triangle" style="top:300px;right:5%;" aria-hidden="true"></div>
    <div class="memphis-shape shape-square" style="top:500px;left:6%;width:40px;height:40px;" aria-hidden="true"></div>
    <div class="memphis-shape shape-squiggle" style="top:80px;right:12%;width:80px;" aria-hidden="true"></div>
    <div class="memphis-shape shape-circle" style="bottom:200px;right:8%;width:35px;height:35px;background:#FF6B9D;" aria-hidden="true"></div>
    <div class="memphis-shape" style="bottom:400px;left:4%;width:12px;height:12px;border-radius:50%;background:#FFE600;opacity:0.35;" aria-hidden="true"></div>
    <div class="memphis-shape shape-squiggle" style="bottom:600px;right:15%;width:60px;opacity:0.12;" aria-hidden="true"></div>

    <section class="www_vvhan_com" aria-label="今日热榜">
      <header role="banner">
        <div class="main">
          <div class="logo">
            <div class="logo-icon" aria-hidden="true">
              <span class="logo-dot"></span>
              <span class="logo-dot"></span>
              <span class="logo-dot"></span>
            </div>
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
        <!-- 头部装饰条 -->
        <div class="header-stripe" aria-hidden="true"></div>
      </header>

      <main id="main-content" role="main">
        <!-- Memphis 公告板块 -->
        <aside class="announcement-section" aria-label="站点公告">
          <div class="announcement-card">
            <!-- 装饰角标 -->
            <div class="announcement-corner" aria-hidden="true">
              <span></span><span></span><span></span>
            </div>
            <div class="announcement-header">
              <div class="announcement-icon-wrap">
                <DrawingPinIcon aria-hidden="true" />
              </div>
              <h3 class="announcement-title">Welcome to Kaixin Hub</h3>
              <a class="announcement-link announcement-link-header"
                href="https://axoxe.com/" target="_blank" rel="noopener noreferrer"
                style="background:#FFE600;border:2px solid #1a1a1a;color:#1a1a1a;margin-left:10px;padding:2px 10px;font-size:13px;font-weight:700;text-decoration:none;display:inline-block!important;white-space:nowrap;line-height:1.5;">
                多云禁止悲观丨KaiXin
              </a>
              <span class="announcement-badge">HOT</span>
            </div>
            <div class="announcement-body">
              <p>
                今日热榜是聚合热榜热搜平台，汇集了各大网站的热榜信息，包括微博热搜、今日头条、知乎日报、澎湃新闻、虎扑步行街、36氪、哔哩哔哩热榜，知乎、IT资讯、虎嗅网、人人都是产品经理、百度、抖音热点豆瓣小组精选等。
              </p>
            </div>
            <!-- 底部装饰点 -->
            <div class="announcement-dots" aria-hidden="true">
              <span></span><span></span><span></span><span></span><span></span>
            </div>
          </div>
        </aside>

        <section class="hotlist" aria-label="热榜列表">
          <ListItem v-for="i in hotlistKey" :key="i.name" :item="i" @refreshFn="refreshFn" />
        </section>
      </main>

      <footer role="contentinfo">
        <div class="footer-stripe" aria-hidden="true">
          <span></span><span></span><span></span><span></span>
        </div>
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
