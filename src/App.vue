<script setup>
import { ref, computed, onMounted } from "vue";
import LevelSelector from './components/LevelSelector.vue';
import OtherWordsDrawer from './components/OtherWordsDrawer.vue';
import CharacterSearch from "./components/CharacterSearch.vue";

import * as HSK1DataSrc from './assets/data/hsk1.json';
import * as HSK2DataSrc from './assets/data/hsk2.json';
import * as HSK3DataSrc from './assets/data/hsk3.json';
import * as HSK4DataSrc from './assets/data/hsk4.json';
import * as HSK5DataSrc from './assets/data/hsk5.json';
import * as HSK6DataSrc from './assets/data/hsk6.json';

const storageKey = "hanziProgress";
const defaultLevel = "hsk1-1";
const levelBreakDownList = {
  "hsk1-1": {
    src: HSK1DataSrc,
    startIndex: 1,
    endIndex: 170,
  },
  "hsk1-2": {
    src: HSK1DataSrc,
    startIndex: 171,
    endIndex: 340,
  },
  "hsk1-3": {
    src: HSK1DataSrc,
    startIndex: 341,
    endIndex: 500,
  },
  "hsk2-1": {
    src: HSK2DataSrc,
    startIndex: 1,
    endIndex: 155,
  },
  "hsk2-2":  {
    src: HSK2DataSrc,
    startIndex: 156,
    endIndex: 310,
  },
  "hsk2-3": {
    src: HSK2DataSrc,
    startIndex: 311,
    endIndex: 465,
  },
  "hsk2-4": {
    src: HSK2DataSrc,
    startIndex: 466,
    endIndex: 619,
  },
  "hsk2-5": {
    src: HSK2DataSrc,
    startIndex: 620,
    endIndex: 772,
  },
  "hsk3-1": {
    src: HSK3DataSrc,
    startIndex: 1,
    endIndex: 163,
  },
  "hsk3-2": {
    src: HSK3DataSrc,
    startIndex: 164,
    endIndex: 325,
  },
  "hsk3-3": {
    src: HSK3DataSrc,
    startIndex: 326,
    endIndex: 487,
  },
  "hsk3-4": {
    src: HSK3DataSrc,
    startIndex: 488,
    endIndex: 649,
  },
  "hsk3-5": {
    src: HSK3DataSrc,
    startIndex: 650,
    endIndex: 811,
  },
  "hsk3-6": {
    src: HSK3DataSrc,
    startIndex: 812,
    endIndex: 973,
  },
  "hsk4-1": {
    src: HSK4DataSrc,
    startIndex: 1,
    endIndex: 167,
  },
  "hsk4-2": {
    src: HSK4DataSrc,
    startIndex: 168,
    endIndex: 334,
  },
  "hsk4-3": {
    src: HSK4DataSrc,
    startIndex: 335,
    endIndex: 501,
  },
  "hsk4-4": {
    src: HSK4DataSrc,
    startIndex: 502,
    endIndex: 668,
  },
  "hsk4-5": {
    src: HSK4DataSrc,
    startIndex: 669,
    endIndex: 834,
  },
  "hsk4-6": {
    src: HSK4DataSrc,
    startIndex: 835,
    endIndex: 1000,
  },
  "hsk5-1": {
    src: HSK5DataSrc,
    startIndex: 1,
    endIndex: 153,
  },
  "hsk5-2": {
    src: HSK5DataSrc,
    startIndex: 154,
    endIndex: 306,
  },
  "hsk5-3": {
    src: HSK5DataSrc,
    startIndex: 307,
    endIndex: 459,
  },
  "hsk5-4": {
    src: HSK5DataSrc,
    startIndex: 460,
    endIndex: 612,
  },
  "hsk5-5": {
    src: HSK5DataSrc,
    startIndex: 613,
    endIndex: 765,
  },
  "hsk5-6": {
    src: HSK5DataSrc,
    startIndex: 766,
    endIndex: 918,
  },
  "hsk5-7": {
    src: HSK5DataSrc,
    startIndex: 919,
    endIndex: 1071,
  },
  "hsk6-1": {
    src: HSK6DataSrc,
    startIndex: 1,
    endIndex: 163,
  },
  "hsk6-2": {
    src: HSK6DataSrc,
    startIndex: 164,
    endIndex: 326,
  },
  "hsk6-3": {
    src: HSK6DataSrc,
    startIndex: 327,
    endIndex: 489,
  },
  "hsk6-4": {
    src: HSK6DataSrc,
    startIndex: 490,
    endIndex: 652,
  },
  "hsk6-5": {
    src: HSK6DataSrc,
    startIndex: 653,
    endIndex: 815,
  },
  "hsk6-6": {
    src: HSK6DataSrc,
    startIndex: 816,
    endIndex: 978,
  },
  "hsk6-7": {
    src: HSK6DataSrc,
    startIndex: 979,
    endIndex: 1140,
  },
};

let totalFullList = {};
Object.keys(levelBreakDownList).forEach((key) => {
  const targetList = levelBreakDownList[key].src.default;
  const startIndex = targetList.findIndex((item) => item.no === `${levelBreakDownList[key].startIndex}`);
  const endIndex = targetList.findIndex((item) => item.no === `${levelBreakDownList[key].endIndex}`);
  const arrSlice = targetList.slice(startIndex, endIndex + 1);
  totalFullList[key] = [...mapList(arrSlice, key)];
});
console.log("Total full list:", totalFullList);

const level = ref("");
const counter = ref(null);
const character = ref(null);
const pinyin = ref(null);
const meaning = ref(null);
const currentListIndex = ref(0);

const isDefinition = ref(false);
const showOtherWords = ref(false);
const showLevelSelector = ref(false);
const searchKey = ref("");
const searchedList = ref({});
const levelSelectorRef = ref(null);
const selectedLevelList = ref([]);

let tempListData = JSON.parse(JSON.stringify(totalFullList)); // NOTE: duplicate a copy for dynamic use
let fullList = [];

const totalLevelList = Object.keys(totalFullList);

function mapList(list, level) {
  return list.map((item) => ({
    level,
    ...item,
  }));
}

function showDefinition(show = true) {
  isDefinition.value = show;
}

function showItem() {
  if (currentListIndex.value < fullList.length) {
    const targetItem = fullList[currentListIndex.value];
    counter.value = targetItem.no;
    level.value = targetItem.level;
    character.value = targetItem.chinese;
    pinyin.value = targetItem.pinyin;
    meaning.value = targetItem.meaning;
  } else {
    alert("No more hanzi");
  }
}

function nextItem() {
  isDefinition.value = false;
  if (currentListIndex.value !== fullList.length) {
    currentListIndex.value++;
  }
  console.log("Current list:", fullList);
  showItem();
}

function previousItem() {
  isDefinition.value = false;
  if (currentListIndex.value > 0) {
    currentListIndex.value--;
  }
  console.log("Current list:", fullList);
  showItem();
}

function levelListChangedHandler({ levelList }) {
  selectedLevelList.value = levelList;
  fullList = [];
  totalLevelList.forEach((levelName) => {
    if (levelList.indexOf(levelName) !== -1) {
      fullList = [
        ...fullList,
        ...tempListData[levelName],
      ]
    }
  });
  currentListIndex.value = 0;
  showItem();
}

function searchCharacterHandler(searchString) {
  checkOtherWords(searchString);
}

function checkOtherWords(character) {
  const tempList = {};
  Object.keys(totalFullList).forEach((key) => {
    const targetKey = properLevelName(key);
    const foundList = totalFullList[key].filter(
      (item) => item.chinese.includes(character)
    );
    if (tempList[targetKey] && tempList[targetKey].length > 0) {
      tempList[targetKey] = [
        ...tempList[targetKey],
        ...foundList,
      ];
    } else {
      tempList[targetKey] = foundList;
    }
  });
  searchKey.value = character;
  searchedList.value = tempList;
  showOtherWords.value = true;
  closeLevelSelector();
}

function closeCheckOtherWords() {
  showOtherWords.value = false;
}

function closeLevelSelector() {
  showLevelSelector.value = false;
}

function openLevelSelector() {
  showLevelSelector.value = true;
}

function properLevelName(level) {
  if (level.includes("-")) {
    return level.substring(0, level.indexOf("-"));
  }
  return level;
}

function storeProgress() {
  const information = {
    selectedLevelList: selectedLevelList.value,
    currentLevel: level.value, 
    currentNo: counter.value,
  };
  localStorage.setItem(storageKey, JSON.stringify(information));
}

function removeProgress() {
  localStorage.removeItem(storageKey);
}

function getStoredProgress() {
  const tempStorage = localStorage.getItem(storageKey);
  if (tempStorage) {
    const {
      selectedLevelList: _selectedLevelList,
      currentLevel,
      currentNo
    } = JSON.parse(tempStorage);
    if (
      _selectedLevelList && 
      _selectedLevelList.length > 0 && 
      currentLevel && 
      currentNo
    ) {
      selectedLevelList.value = _selectedLevelList;
      levelListChangedHandler({ levelList: selectedLevelList.value });
      const currentItemIndex = fullList.findIndex(
        (item) => item.no === currentNo && item.level === currentLevel
      );
      if (currentItemIndex) {
        currentListIndex.value = currentItemIndex;
      }
    } else {
      selectedLevelList.value = [defaultLevel];
      levelListChangedHandler({ levelList: selectedLevelList.value });
    }
  } else {
    selectedLevelList.value = [defaultLevel];
    levelListChangedHandler({ levelList: selectedLevelList.value });
  }
}

function randomiseList() {
  for (let i = fullList.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [fullList[i], fullList[j]] = [fullList[j], fullList[i]];
  }
  showItem();
}

onMounted(() => {
  getStoredProgress();
  showItem();
  document.addEventListener("click", (e) => {
    const targetButton = e.target.closest("button");
    if (targetButton) {
      e.target.blur();
      document.body.focus();
    }
  });
  document.addEventListener("keydown", (e) => {
    if (e.code === "ArrowRight") {
      nextItem();
    }
    if (e.code === "ArrowLeft") {
      previousItem();
    }
    if (e.code === "Space") {
      if (!isDefinition.value) {
        showDefinition(true);
      }
    }
  });
  document.addEventListener("keyup", (e) => {
    if (e.code === "Space") {
      if (isDefinition.value) {
        showDefinition(false);
      }
    }
  });
});
</script>

<template>
  <div class="display" :class="{ 'defined': isDefinition }">
    <div class="character">
      <div class="text-wrapper" :class="`n-${character?.length || 1}`">
        <button 
          v-for="c in character"
          @click="checkOtherWords(c)"
        >
          {{ c }}
        </button>
      </div>
    </div>
    <button class="definition" @click="showDefinition()">
      <div class="pinyin">{{ pinyin }}</div>
      <div class="meaning">{{ meaning }}</div>
    </button>
    <div class="level-indicator">
      <strong :class="`text-${properLevelName(level)}`">{{ properLevelName(level) }}</strong>
      (No: {{ counter }}) {{ fullList.length - currentListIndex }}/{{ fullList.length }}
    </div>
    <button class="btn save" @click="storeProgress" title="Save">
      <svg 
        xmlns="http://www.w3.org/2000/svg" 
        height="24px" 
        viewBox="0 -960 960 960" 
        width="24px" 
        fill="#fff"
      >
        <path d="M200-120v-640q0-33 23.5-56.5T280-840h240v80H280v518l200-86 200 86v-278h80v400L480-240 200-120Zm80-640h240-240Zm400 160v-80h-80v-80h80v-80h80v80h80v80h-80v80h-80Z" />
      </svg>
    </button>
    <button class="btn unsave" @click="removeProgress" title="Save">
      <svg 
        xmlns="http://www.w3.org/2000/svg" 
        height="24px" 
        viewBox="0 -960 960 960" 
        width="24px" 
        fill="#fff"
      >
        <path d="M840-680H600v-80h240v80ZM200-120v-640q0-33 23.5-56.5T280-840h240v80H280v518l200-86 200 86v-278h80v400L480-240 200-120Zm80-640h240-240Z" />
      </svg>
    </button>
    <button class="btn randomise" @click="randomiseList" title="Randomise">
      <svg 
        xmlns="http://www.w3.org/2000/svg" 
        height="24px" 
        viewBox="0 -960 960 960" 
        width="24px" 
        fill="#fff"
      >
        <path d="M560-160v-80h104L537-367l57-57 126 126v-102h80v240H560Zm-344 0-56-56 504-504H560v-80h240v240h-80v-104L216-160Zm151-377L160-744l56-56 207 207-56 56Z" />
      </svg>
    </button>
    <CharacterSearch class="search" @search-triggered="searchCharacterHandler" />
  </div>
  <button class="btn define" @click="showDefinition(!isDefinition)" title="Define">
    <svg 
      xmlns="http://www.w3.org/2000/svg" 
      height="48px" 
      viewBox="0 -960 960 960" 
      width="48px" 
      fill="#fff"
    >
      <path d="m298-262-56-56 121-122H80v-80h283L242-642l56-56 218 218-218 218Zm222-18v-80h360v80H520Zm0-320v-80h360v80H520Zm120 160v-80h240v80H640Z" />
    </svg>
  </button>
  <button class="btn previous" @click="previousItem()" title="Previous">
    <svg 
      xmlns="http://www.w3.org/2000/svg" 
      height="48px" 
      viewBox="0 -960 960 960" 
      width="48px" 
      fill="#fff"
    >
      <path d="M220-240v-480h80v480h-80Zm520 0L380-480l360-240v480Zm-80-240Zm0 90v-180l-136 90 136 90Z" />
    </svg>
  </button>
  <button class="btn next" @click="nextItem()" title="Next">
    <svg 
      xmlns="http://www.w3.org/2000/svg" 
      height="48px" 
      viewBox="0 -960 960 960" 
      width="48px" 
      fill="#fff"
    >
      <path d="M660-240v-480h80v480h-80Zm-440 0v-480l360 240-360 240Zm80-240Zm0 90 136-90-136-90v180Z" />
    </svg>
  </button>
  <LevelSelector 
    ref="levelSelectorRef" 
    :level-list="totalLevelList"
    :selected-level-list="selectedLevelList"
    :display="showLevelSelector" 
    @levelListChanged="levelListChangedHandler" 
    @close="closeLevelSelector" 
    @open="openLevelSelector" 
  />
  <OtherWordsDrawer 
    :display="showOtherWords" 
    :search-key="searchKey" 
    :data-list="searchedList" 
    @close="closeCheckOtherWords" 
  />
</template>

<style lang="scss" scoped>
.display {
  position: relative;
  height: calc(100vh - var(--button-height));

  .btn.save,
  .btn.unsave,
  .btn.randomise {
    position: absolute;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    height: var(--mini-button-height);
    width: var(--mini-button-width);
    padding: var(--small-padding);
    font-size: 1.5rem;
    cursor: pointer;
  }
  
  .btn.save {
    top: 0;
  }

  .btn.unsave {
    top: var(--mini-button-height);
  }

  .btn.randomise {
    top: calc(var(--mini-button-height) * 2);
  }

  .search {
    position: absolute;
    top: calc(var(--mini-button-height) * 3);
    right: 0;
  }

  .level-indicator { 
    padding: var(--general-padding);
    pointer-events: none;
    text-align: center;
    
    > strong {
      text-transform: uppercase;
    }
  }

  .character,
  .definition {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }

  .character {
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 13rem;

    .text-wrapper {
      text-align: center;

      > button {
        background: transparent;
        font-size: inherit;
        display: inline;
      }
  
      &.n-2 {
        font-size: 9rem;
      }
  
      &.n-3 {
        font-size: 8rem;
      }
  
      &.n-4 {
        font-size: 5rem;
      }
  
      &.n-5 {
        font-size: 5rem;
      }
  
      &.n-6 {
        font-size: 5rem;
      }
  
      &.n-7 {
        font-size: 5rem;
      }
  
      &.n-8 {
        font-size: 6rem;
      }
    }
  }

  .definition {
    display: none;
    padding: var(--general-padding);

    .pinyin {
      font-size: 3rem;
      margin-bottom: 1rem;
    }
    
    .meaning {
      font-size: 2rem;
    }
  }

  &.defined {
    .character {
      display: none;
    }

    .definition {
      display: block;
    }
  }
}

.btn.previous,
.btn.next,
.btn.define {
  position: fixed;
  display: flex;
  align-items: center;
  justify-content: center;
  height: var(--button-height);
}

.btn.previous {
  right: 35vw;
  width: 35vw;
  bottom: var(--free-site-bar-height);
}

.btn.next {
  right: 0;
  width: 35vw;
  bottom: var(--free-site-bar-height);
}

.btn.define {
  left: 0;
  width: 30vw;
  bottom: var(--free-site-bar-height);
}

.drawer {
  position: fixed;
  
  .search-key {
    height: var(--selector-height);
  }

  ul.entry-list {
    height: calc(100vh - var(--selector-height));
    list-style: none;
    padding: 0;
    margin: 0;
    overflow: auto;
    
    > li {
      display: block;
      
      > a {
        display: block;
        padding: var(--general-padding);
      }
    }
  }
}
</style>
