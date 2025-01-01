<template>
  <div class="app" :class="{ 'dark': isDark }">
    <div class="theme-toggle" @click="toggleTheme">
      <svg v-if="isDark" class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <circle cx="12" cy="12" r="5"/>
        <line x1="12" y1="1" x2="12" y2="3"/>
        <line x1="12" y1="21" x2="12" y2="23"/>
        <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"/>
        <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"/>
        <line x1="1" y1="12" x2="3" y2="12"/>
        <line x1="21" y1="12" x2="23" y2="12"/>
        <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"/>
        <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"/>
      </svg>
      <svg v-else class="icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"/>
      </svg>
    </div>
    <div class="content">
      <div v-for="(line, lineIndex) in textLines" :key="lineIndex" class="line">
        <template v-if="shouldShowLine(lineIndex)">
          <span
            v-for="(char, charIndex) in splitTextIntoChars(line)"
            :key="charIndex"
            class="char"
            :class="{ animate: shouldShowChar(lineIndex, charIndex) }"
            v-html="char"
          >
          </span>
        </template>
      </div>
    </div>
    <div class="copyright">
      <p id="commitHash"></p>
      Copyright {{ startYear }}-{{ currentYear }} 
      <a href="https://juz1.cn" target="_blank" rel="noopener">Juzi</a>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const isDark = ref(false)
const currentLineIndex = ref(0)
const currentCharIndex = ref(-1)
const startYear = 2024
const currentYear = computed(() => new Date().getFullYear())

const textLines = ref([
  'Welcome to Juzi\'s Site',
  'I\'m a frontend <Developer />',
  'You can contact me at i@juz1.cn',
  '永远相信美好的事情即将发生'
])

const splitTextIntoChars = (text) => {
  return text.split('').map(char => char === ' ' ? '&nbsp;' : char)
}

const shouldShowChar = (lineIndex, charIndex) => {
  if (lineIndex < currentLineIndex.value) return true
  if (lineIndex === currentLineIndex.value) {
    return charIndex <= currentCharIndex.value
  }
  return false
}

const toggleTheme = () => {
  isDark.value = !isDark.value
  document.body.classList.toggle('dark')
}

const shouldShowLine = (index) => {
  return index <= currentLineIndex.value
}

const checkSystemTheme = () => {
  const darkModeMediaQuery = window.matchMedia('(prefers-color-scheme: dark)')
  const updateTheme = (e) => {
    isDark.value = e.matches
    document.body.classList.toggle('dark', e.matches)
  }
  darkModeMediaQuery.addEventListener('change', updateTheme)
  updateTheme(darkModeMediaQuery)
}

onMounted(() => {
  checkSystemTheme()
  
  const showNextChar = () => {
    const currentLine = textLines.value[currentLineIndex.value]
    if (!currentLine) return

    if (currentCharIndex.value < currentLine.length - 1) {
      setTimeout(() => {
        currentCharIndex.value++
        showNextChar()
      }, 120)
    } else {
      if (currentLineIndex.value < textLines.value.length - 1) {
        setTimeout(() => {
          currentLineIndex.value++
          currentCharIndex.value = -1
          showNextChar()
        }, 600)
      }
    }
  }
  
  showNextChar()
})
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap');

body {
  margin: 0;
  transition: background-color 0.3s, color 0.3s;
  font-family: 'Inter', sans-serif;
}

body.dark {
  background-color: rgb(17, 17, 17);
  color: rgba(255, 255, 255, 0.9);
}

.app {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  background-color: rgb(253, 253, 253);
  color: rgba(0, 0, 0, 0.9);
  transition: background-color 0.3s, color 0.3s;
}

.app.dark {
  background-color: rgb(17, 17, 17);
  color: rgba(255, 255, 255, 0.9);
}

.theme-toggle {
  position: absolute;
  top: 20px;
  right: 20px;
  cursor: pointer;
  user-select: none;
}

.icon {
  width: 24px;
  height: 24px;
  transition: transform 0.3s ease;
}

.theme-toggle:hover .icon {
  transform: rotate(15deg);
}

.content {
  text-align: center;
  white-space: nowrap;
  overflow: hidden;
}

.line {
  margin: 10px 0;
  height: 1.5em;
  overflow: hidden;
}

.char {
  display: inline-block;
  opacity: 0;
  transform: translateY(15px);
  font-weight: 500;
}

.char.animate {
  animation: bounceIn 0.4s cubic-bezier(0.12, 0.8, 0.32, 1.2) forwards;
}

.copyright {
  position: absolute;
  bottom: 20px;
  text-align: center;
  font-size: 12px;
  width: 100%;
  color: rgba(0, 0, 0, 0.4);
}

.app.dark .copyright {
  color: rgba(255, 255, 255, 0.4);
}

.copyright a {
  color: inherit;
  text-decoration: none;
  border-bottom: 1px dashed currentColor;
  padding-bottom: 1px;
  transition: opacity 0.3s;
}

.copyright a:hover {
  opacity: 0.7;
}

@keyframes bounceIn {
  0% {
    opacity: 0;
    transform: translateY(15px);
  }
  60% {
    opacity: 0.7;
    transform: translateY(-4px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
