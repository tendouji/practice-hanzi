<script setup>
import { ref } from 'vue'

const props = defineProps({
  display: {
    type: Boolean,
    default: false,
  },
  searchKey: {
    type: String,
    default: "",
  },
  dataList: {
    type: Object,
    default: () => {},
  }
})

const emits = defineEmits(["close"]);

function closeDrawer() {
  emits("close");
}
</script>

<template>
  <div class="other-words-drawer" :class="{ 'displayed': display }">
    <div class="overlay" @click="closeDrawer"></div>
    <div class="drawer">
      <div class="search-key">
        Other words containing
        <div class="text">{{ searchKey }}</div>
      </div>
      <ul v-if="Object.keys(dataList).length > 0" class="search-list">
        <template v-for="level in Object.keys(dataList)">
          <li v-if="dataList[level].length > 0" class="level-item">
            <div class="level" :class="`bg-${level}`">{{ level }}</div>
            <ul class="item-list">
              <li v-for="item in dataList[level]" class="item">
                <span class="chinese" :class="`text-${level}`">{{ item.chinese }}</span>
                <span class="pinyin">{{ item.pinyin }}</span>
                <div class="meaning">{{ item.meaning }}</div>
              </li>
            </ul>
          </li>
        </template>
      </ul>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.other-words-drawer {
  position: fixed;
  top: 0;
  left: 0;
  height: calc(100% - var(--free-site-bar-height));
  width: 100vw;
  pointer-events: none;
  overflow: hidden;
  
  .drawer {
    position: absolute;
    top: 0;
    left: 100%;
    height: 100%;
    width: calc(100vw - var(--drawer-gap));
    background-color: var(--bg-color);
    transition: left 200ms;

    .search-key {
      height: var(--selector-height);
      padding: var(--general-padding);
      border-bottom: var(--general-border);
      text-align: center;
      box-sizing: border-box;

      .text {
        font-size: 1.5em;
        font-weight: bold;
        color: var(--primary-color);
      }
    }

    ul.search-list {
      height: calc(100% - var(--selector-height));
      padding: 0;
      margin: 0;
      list-style: none;
      overflow: auto;
      
      li.level-item {
        display: block;
        border-bottom: var(--general-border);
        
        .level {
          padding: var(--general-padding);
          text-transform: uppercase;
          font-weight: bold;
        }

        ul.item-list {
          padding: 0;
          margin: 0;
          list-style: none;
      
          li.item {
            display: block;
            padding: var(--general-padding);
            border-top: var(--general-border-light);

            .chinese {
              font-weight: bold;
              margin-right: var(--general-padding);
            }
          }
        }
      }
    }
  }

  &.displayed {
    pointer-events: visible;
    
    .overlay {
      opacity: 1;
    }
    
    .drawer {
      left: var(--drawer-gap);
    }
  }
}
</style>
