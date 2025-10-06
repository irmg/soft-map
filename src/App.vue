<script setup>
import { ref, computed } from 'vue'
import { softwareList } from './softwareData.js'

// 获取所有分类
const categories = computed(() => {
  const uniqueCategories = [...new Set(softwareList.map(item => item.category))]
  return ['全部', ...uniqueCategories]
})

// 当前选中的分类
const selectedCategory = ref('全部')

// 搜索关键词
const searchQuery = ref('')

// 过滤后的软件列表
const filteredSoftwareList = computed(() => {
  let result = softwareList
  
  // 按分类过滤
  if (selectedCategory.value !== '全部') {
    result = result.filter(software => software.category === selectedCategory.value)
  }
  
  // 按搜索关键词过滤
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase()
    result = result.filter(software => 
      software.name.toLowerCase().includes(query) || 
      software.description.toLowerCase().includes(query) ||
      software.category.toLowerCase().includes(query)
    )
  }
  
  return result
})

// 按分类组织软件列表
const softwareByCategory = computed(() => {
  // 当有搜索关键词时，显示在"搜索结果"分类下
  if (searchQuery.value) {
    return { '搜索结果': filteredSoftwareList.value }
  }
  
  // 当选择特定分类时，只显示该分类
  if (selectedCategory.value !== '全部') {
    return { [selectedCategory.value]: filteredSoftwareList.value }
  }
  
  // 当选择"全部"且无搜索时，按分类组织显示
  const grouped = {}
  filteredSoftwareList.value.forEach(software => {
    if (!grouped[software.category]) {
      grouped[software.category] = []
    }
    grouped[software.category].push(software)
  })
  
  return grouped
})

// 跳转到软件官网
const goToSoftware = (url) => {
  window.open(url, '_blank')
}

// 设置选中的分类
const selectCategory = (category) => {
  selectedCategory.value = category
  searchQuery.value = ''
}

// 清空搜索
const clearSearch = () => {
  searchQuery.value = ''
  selectedCategory.value = '全部'
}

// 联系邮箱
const contactEmail = 'irmg@foxmail.com'
</script>

<template>
  <div class="container">
    <!-- 顶部导航栏 -->
    <nav class="top-nav">
      <div class="nav-content">
        <h1 class="site-title">正版软件导航站</h1>
        <div class="search-container">
          <input 
            type="text" 
            v-model="searchQuery" 
            placeholder="搜索软件、分类或功能... (在'全部'标签下搜索可获得完整结果)" 
            class="search-input"
          />
          <div class="search-icon">🔍</div>
        </div>
        <div class="spacer"></div>
      </div>
    </nav>

    <main>
      <!-- 页面顶部说明 -->
      <div class="footer-notes top-notes">
        <div class="disclaimer">
          <h3>免责声明</h3>
          <p>本站不提供任何软件下载，也不提供任何软件的破解方式。所有软件图标和信息仅用于指引用户前往官方下载页面。</p>
        </div>
        
        <div class="contact-info">
          <h3>联系我们</h3>
          <p>如果您需要其他软件的官方下载链接，请发送邮件至：<a :href="'mailto:' + contactEmail">{{ contactEmail }}</a></p>
        </div>
      </div>

      <!-- 分类标签 -->
      <div class="categories-section">
        <div class="categories">
          <span 
            v-for="category in categories" 
            :key="category"
            class="category-tag"
            :class="{ active: selectedCategory === category }"
            @click="selectCategory(category)"
          >
            {{ category }}
          </span>
        </div>
        <div class="search-hint" v-if="searchQuery">
          <p>💡 搜索时请确保在"全部"标签下进行搜索以获得完整结果</p>
        </div>
      </div>

      <!-- 软件列表 -->
      <div class="software-section">
        <div v-for="(softwares, category) in softwareByCategory" :key="category" class="category-group">
          <h2 class="category-title">{{ category }}</h2>
          <div class="software-list">
            <div 
              v-for="software in softwares" 
              :key="software.id" 
              class="software-item"
              @click="goToSoftware(software.url)"
            >
              <div class="software-icon">
                <img :src="software.icon" :alt="software.name" class="software-logo" />
              </div>
              <div class="software-content">
                <h3 class="software-name">{{ software.name }}</h3>
                <p class="description">{{ software.description }}</p>
                <div class="footer">
                  <span class="category-badge">{{ software.category }}</span>
                  <span class="go-link">前往官网 ➔</span>
                </div>
              </div>
            </div>
          </div>
        </div>
        
        <!-- 搜索结果为空时显示 -->
        <div v-if="filteredSoftwareList.length === 0" class="no-results">
          <div class="no-results-content">
            <div class="sad-icon">😢</div>
            <h3>未找到相关软件</h3>
            <p>抱歉，没有找到与 "{{ searchQuery }}" 相关的软件</p>
            <button class="clear-search" @click="clearSearch">清空搜索条件</button>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<style scoped>
.container {
  width: 100%;
  max-width: 100%;
  margin: 0;
  padding: 0;
}

.top-nav {
  background: white;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  position: sticky;
  top: 0;
  z-index: 100;
  width: 100%;
}

.nav-content {
  display: flex;
  align-items: center;
  padding: 15px 30px;
  max-width: 100%;
  margin: 0 auto;
}

.site-title {
  font-size: 1.8rem;
  margin: 0;
  color: #2c3e50;
  white-space: nowrap;
}

.search-container {
  position: relative;
  flex: 1;
  max-width: 600px;
  margin: 0 20px;
}

.search-input {
  width: 100%;
  padding: 12px 20px 12px 50px;
  font-size: 1rem;
  border: 2px solid #e2e8f0;
  border-radius: 50px;
  outline: none;
  transition: all 0.3s ease;
}

.search-input:focus {
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-icon {
  position: absolute;
  left: 20px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 1.2rem;
  color: #7f8c8d;
}

.spacer {
  width: 100px; /* 与标题宽度大致相同，用于保持搜索框居中 */
}

.categories-section {
  margin: 30px;
  width: 100%;
}

.categories {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}

.search-hint {
  margin-top: 15px;
  padding: 10px 15px;
  background-color: #e3f2fd;
  border-radius: 8px;
  color: #1976d2;
  font-size: 0.9rem;
}

.search-hint p {
  margin: 0;
  display: flex;
  align-items: center;
  gap: 8px;
}

.top-notes {
  margin: 20px 30px 0;
}

@media (max-width: 768px) {
  .top-notes {
    margin: 15px;
  }
}

.category-tag {
  padding: 8px 20px;
  background: #f1f5f9;
  color: #475569;
  border-radius: 25px;
  font-size: 0.95rem;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 1px solid #e2e8f0;
}

.category-tag:hover {
  background: #e2e8f0;
  transform: translateY(-2px);
}

.category-tag.active {
  background: #667eea;
  color: white;
  border-color: #667eea;
}

.search-hint {
  margin-top: 15px;
  padding: 10px 15px;
  background-color: #e3f2fd;
  border-radius: 8px;
  color: #1976d2;
  font-size: 0.9rem;
}

.search-hint p {
  margin: 0;
  display: flex;
  align-items: center;
  gap: 8px;
}

.software-section {
  padding: 0 30px 30px;
  width: 100%;
}

.category-group {
  margin-bottom: 40px;
  width: 100%;
}

.category-title {
  font-size: 1.5rem;
  color: #2c3e50;
  margin-bottom: 20px;
  padding-bottom: 10px;
  border-bottom: 2px solid #e2e8f0;
}

.software-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 25px;
  width: 100%;
}

.software-item {
  border-radius: 15px;
  overflow: hidden;
  cursor: pointer;
  transition: all 0.4s ease;
  background: white;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.08);
  display: flex;
  padding: 20px;
  border: 1px solid #eee;
}

.software-item:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  border-color: #667eea;
}

.software-icon {
  width: 48px;
  height: 48px;
  margin-right: 15px;
  align-self: flex-start;
  display: flex;
  align-items: center;
  justify-content: center;
}

.software-logo {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}

.software-content {
  flex: 1;
}

.software-name {
  margin: 0 0 10px 0;
  color: #2c3e50;
  font-size: 1.3rem;
  font-weight: 600;
}

.description {
  color: #7f8c8d;
  margin-bottom: 20px;
  font-size: 0.95rem;
  line-height: 1.6;
}

.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.category-badge {
  display: inline-block;
  padding: 5px 12px;
  background-color: #e0f7fa;
  color: #00838f;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: bold;
}

.go-link {
  color: #667eea;
  font-weight: 600;
  font-size: 0.9rem;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.software-item:hover .go-link {
  opacity: 1;
}

.no-results {
  grid-column: 1 / -1;
  text-align: center;
  padding: 60px 20px;
  background: white;
  border-radius: 15px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.08);
  margin: 0 30px;
}

.no-results-content {
  max-width: 500px;
  margin: 0 auto;
}

.sad-icon {
  font-size: 4rem;
  margin-bottom: 20px;
}

.no-results h3 {
  font-size: 1.8rem;
  color: #2c3e50;
  margin-bottom: 10px;
}

.no-results p {
  color: #7f8c8d;
  font-size: 1.1rem;
  margin-bottom: 25px;
}

.clear-search {
  padding: 12px 24px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 25px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.clear-search:hover {
  transform: translateY(-3px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
}

/* 底部说明区域 */
.footer-notes {
  background: #f8fafc;
  border-top: 1px solid #e2e8f0;
  padding: 40px 30px 30px;
  margin-top: 40px;
}

.disclaimer, .contact-info {
  max-width: 800px;
  margin: 0 auto 20px;
  padding: 20px;
  background: white;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
}

.disclaimer h3, .contact-info h3 {
  color: #2c3e50;
  margin-top: 0;
  margin-bottom: 15px;
  font-size: 1.3rem;
}

.disclaimer p, .contact-info p {
  color: #7f8c8d;
  line-height: 1.6;
  margin: 0;
}

.contact-info a {
  color: #667eea;
  text-decoration: none;
  font-weight: 600;
}

.contact-info a:hover {
  text-decoration: underline;
}

@media (max-width: 1200px) {
  .software-list {
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  }
}

@media (max-width: 768px) {
  .nav-content {
    flex-direction: column;
    gap: 15px;
    padding: 15px;
  }
  
  .site-title {
    align-self: flex-start;
  }
  
  .search-container {
    width: 100%;
    margin: 0;
  }
  
  .spacer {
    display: none;
  }
  
  .categories-section {
    margin: 20px 15px;
  }
  
  .software-section {
    padding: 0 15px 20px;
  }
  
  .software-list {
    grid-template-columns: 1fr;
    gap: 20px;
  }
  
  .software-item {
    padding: 15px;
  }
  
  .software-name {
    font-size: 1.2rem;
  }
  
  .categories {
    gap: 8px;
  }
  
  .category-tag {
    font-size: 0.9rem;
    padding: 6px 16px;
  }
  
  .no-results {
    margin: 0 15px;
  }
  
  .footer-notes {
    padding: 30px 15px 20px;
  }
}

@media (max-width: 480px) {
  .software-list {
    gap: 15px;
  }
  
  .software-item {
    flex-direction: column;
    text-align: center;
  }
  
  .software-icon {
    margin-right: 0;
    margin-bottom: 10px;
    text-align: center;
  }
  
  .footer {
    flex-direction: column;
    gap: 10px;
  }
  
  .go-link {
    opacity: 1;
  }
  
  .category-title {
    font-size: 1.3rem;
  }
  
  .disclaimer, .contact-info {
    padding: 15px;
  }
}
</style>