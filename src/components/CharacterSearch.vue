<script setup>
import { ref } from 'vue';

const display = ref(false);
const searchCharacterInputRef = ref();
const searchKey = ref("");

const emits = defineEmits(["searchTriggered"]);


function searchCharacter() {
  emits("searchTriggered", searchKey.value);
}

function openCloseSearch() {
  if (display.value) {
    display.value = false;
  } else {
    display.value = true
    searchCharacterInputRef.value.focus();
  }
}
</script>

<template>
  <div class="character-search-wrapper" :class="{ 'displayed': display }">
    <div class="search-form">
      <div class="input">
        <input 
          v-model="searchKey"
          ref="searchCharacterInputRef"
          type="text" 
          @keyup.enter="searchCharacter"
        />
      </div>
      <button class="btn search" @click="searchCharacter" title="Search">
        <svg 
          xmlns="http://www.w3.org/2000/svg" 
          height="24px" 
          viewBox="0 -960 960 960" 
          width="24px" 
          fill="#fff"
        >
          <path d="M784-120 532-372q-30 24-69 38t-83 14q-109 0-184.5-75.5T120-580q0-109 75.5-184.5T380-840q109 0 184.5 75.5T640-580q0 44-14 83t-38 69l252 252-56 56ZM380-400q75 0 127.5-52.5T560-580q0-75-52.5-127.5T380-760q-75 0-127.5 52.5T200-580q0 75 52.5 127.5T380-400Z" />
        </svg>
      </button>
    </div>
    <button class="btn open-search" @click="openCloseSearch" title="Find Character">
      <svg 
        xmlns="http://www.w3.org/2000/svg" 
        height="24px" 
        viewBox="0 -960 960 960" 
        width="24px" 
        fill="#fff"
      >
        <path d="M160-391h45l23-66h104l24 66h44l-97-258h-46l-97 258Zm81-103 38-107h2l38 107h-78Zm319-70v-68q33-14 67.5-21t72.5-7q26 0 51 4t49 10v64q-24-9-48.5-13.5T700-600q-38 0-73 9.5T560-564Zm0 220v-68q33-14 67.5-21t72.5-7q26 0 51 4t49 10v64q-24-9-48.5-13.5T700-380q-38 0-73 9t-67 27Zm0-110v-68q33-14 67.5-21t72.5-7q26 0 51 4t49 10v64q-24-9-48.5-13.5T700-490q-38 0-73 9.5T560-454ZM260-320q47 0 91.5 10.5T440-278v-394q-41-24-87-36t-93-12q-36 0-71.5 7T120-692v396q35-12 69.5-18t70.5-6Zm260 42q44-21 88.5-31.5T700-320q36 0 70.5 6t69.5 18v-396q-33-14-68.5-21t-71.5-7q-47 0-93 12t-87 36v394Zm-40 118q-48-38-104-59t-116-21q-42 0-82.5 11T100-198q-21 11-40.5-1T40-234v-482q0-11 5.5-21T62-752q46-24 96-36t102-12q58 0 113.5 15T480-740q51-30 106.5-45T700-800q52 0 102 12t96 36q11 5 16.5 15t5.5 21v482q0 23-19.5 35t-40.5 1q-37-20-77.5-31T700-240q-60 0-116 21t-104 59ZM280-499Z" />
      </svg>
    </button>
  </div>
</template>

<style lang="scss" scoped>
.character-search-wrapper {
  position: relative;
  height: var(--mini-button-height);
  width: var(--mini-button-height);
  overflow: hidden;

  .search-form,
  .search-form .input,
  .search-form .btn.search,
  .btn.open-search {
    position: absolute;
    top: 0;
    height: var(--mini-button-height);
  }

  .search-form .btn.search,
  .btn.open-search {
    display: flex;
    align-items: center;
    justify-content: center;
    width: var(--mini-button-width);
    padding: var(--small-padding);
    font-size: 1.5rem;
    cursor: pointer;
  }

  .search-form {
    right: -calc(var(--mini-button-width));
    width: calc(var(--mini-button-width) * 2.5);

    .input {
      right: var(--mini-button-width);
      width: calc(var(--mini-button-width) * 1.5);
      
      input[type="text"] {
        width: 100%;
        height: 100%;
        padding: var(--general-padding);
        box-sizing: border-box;
        border: 1px solid var(--primary-color-light);
        border-radius: 0;

        &:active,
        &:focus {
          outline: none;
        }
      }
    }
  
    .btn.search {
      right: 0;
    }
  }

  .btn.open-search {
    right: 0;
  }

  &.displayed {
    width: calc(var(--mini-button-width) * 3.5);

    .search-form {
      right: calc(var(--mini-button-width));
    }
  }
}
</style>
