<script setup>
const props = defineProps({
  display: {
    type: Boolean,
    default: false,
  },
  levelList: {
    type: Array,
    default: [],
  },
  selectedLevelList: {
    type: Array,
    default: [],
  },
})

const emits = defineEmits(["levelListChanged", "close", "open"]);

function updateSelectedLevel(e) {
  const el = e.target;
  const { checked: isChecked, name: level } = el;
  const tempSelectedLevelList = [...props.selectedLevelList];

  if (isChecked) {
    if (tempSelectedLevelList.indexOf(level) === -1) {
      tempSelectedLevelList.push(level);
    }
  } else {
    const targetIndex = tempSelectedLevelList.indexOf(level);
    if (targetIndex !== -1) {
      tempSelectedLevelList.splice(targetIndex, 1);
    }
  }
  emits("levelListChanged", { levelList: tempSelectedLevelList.sort() });
}

function isSelected(level) {
  const targetIndex = props.selectedLevelList.indexOf(level);
  return targetIndex !== -1;
}

function closeDrawer() {
  emits("close");
}

function openDrawer() {
  emits("open");
}

function properLevelName(level) {
  if (level.includes("-")) {
    return level.substring(0, level.indexOf("-"));
  }
  return level;
}
</script>

<template>
  <button class="open-level-selector-drawer" @click="openDrawer">
    <template v-if="selectedLevelList.length > 0">
      <div 
        v-for="l in selectedLevelList"
        :key="`n-${l}`"
        :class="`text-${properLevelName(l)}`"
      >
        {{ l }}
      </div>
    </template>
    <template v-else>0 selected</template>
  </button>
  <div class="level-selector-drawer" :class="{ 'displayed': display }">
    <div class="overlay" @click="closeDrawer"></div>
    <div class="drawer">
      <ul>
        <li v-for="level in levelList" :key="level">
          <label :class="`bg-${properLevelName(level)}`" :for="level">
            {{ level }}
            <input 
              type="checkbox" 
              :id="level" 
              :name="level" 
              :checked="isSelected(level)"
              @change="updateSelectedLevel" 
            />
          </label>
        </li>
        <li>
          Build by <a href="https://www.instagram.com/tendouji" target="_blank">Tendouji</a> ⟋
          <a href="https://www.instagram.com/tendouji.art" target="_blank">art</a> • 2024
        </li>
      </ul>
    </div>    
  </div>
</template>

<style lang="scss" scoped>
.open-level-selector-drawer {
  position: fixed;
  top: 0;
  left: 0;
  background-color: var(--light-bg-color);
  padding: var(--general-padding);
  font-size: 0.75rem;
}

.level-selector-drawer {
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
    right: 100%;
    height: 100%;
    width: calc(100vw - var(--drawer-gap));
    padding: var(--general-padding);
    background-color: var(--bg-color);
    box-sizing: border-box;
    overflow: auto;
    transition: right 200ms;

    > ul {
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      gap: 5px;
      padding: 0;
      margin: 0;
      list-style: none;

      > li {
        display: inline-block;

        > label {
          display: block;
          border-radius: 2px;
          padding: 5px;
          text-align: center;
          white-space: no-wrap;
          text-transform: uppercase;
          font-weight: bold;
          cursor: pointer;
        }

        &:last-child {
          text-align: center;
          margin-top: auto;
          font-size: 12px;
          font-weight: bold;
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
      right: var(--drawer-gap);
    }
  }
}
</style>
