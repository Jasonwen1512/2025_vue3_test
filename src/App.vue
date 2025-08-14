<script setup>
import { ref, onMounted, computed } from "vue";
// 任務1. 引入FavList組件到App.vue的aside中
import FavList from "./components/FavList.vue";

// 如果使用pinia
// import { useFavoriteStore } from '@/stores/favorites'
// const favoriteStore = useFavoriteStore()

// 任務2. 顯示專輯資料
// 目前畫面中僅呈現defaultData
// 請將資料替換成`public/albums.json`中的專輯資料

const defaultData = {
  id: 1,
  images: "https://i.scdn.co/image/ab67616d00001e023e59f3e73b99ed248ab7bae2",
  name: "Day & Night (feat. Jay Park)",
  artists: "Lee Young Ji",
};
let albmusData = ref(null);
onMounted(async () => {
  let response = await fetch("src/assets/albums.json");
  albmusData.value = await response.json();
  console.log(albmusData);
});

// 任務3:開啟關閉側拉選單(收藏列表)
const asideToggle = ref(false);
const toggleAside = () => {
  asideToggle.value = !asideToggle.value;
};

// 任務4.專輯資料可以被input搜尋
const search = ref("");
const searchData = computed(() => {
  if (search.value.trim() === "") {
    return albmusData.value;
  } else {
    let filterList = [];
    albmusData.value.forEach((item) => {
      // 以name做篩選
      if (item.name.includes(search.value)) {
        filterList.push(item);
      }
    });
    return filterList;
  }
});

// 任務5.加入我的收藏
// 不限定方式，如果不知道怎麼使用pinia可以用其他方式
import { useFavoriteStore } from "@/stores/favorites";
const favoriteStore = useFavoriteStore();
const addFav = (item) => {
  favoriteStore.addFav(item);
};
</script>

<template>
  <header>
    <div>
      <input type="search" v-model="search" />
      <button @click="toggleAside">
        <img src="~@/assets/heartRed.png" alt="收藏列表" />
      </button>
    </div>
  </header>

  <main>
    <div v-for="(item, index) in searchData" :key="index">
      <div class="card">
        <img :src="item.images" />
        <div class="card_body">
          <h6>{{ item.name }}</h6>
          <p>{{ item.artists }}</p>
        </div>
        <div class="card_footer">
          <button class="favoriteBtn" @click="addFav(item)">
            <img src="~@/assets/heartBlack.png" alt="收藏專輯" />
          </button>
        </div>
      </div>
    </div>
  </main>

  <aside :class="{ open: asideToggle }">
    <!-- 收藏清單 -->
    <FavList />
  </aside>
</template>

<style lang="scss">
button {
  width: 2rem;
  height: 2rem;
  border-radius: 2rem;
  padding: 0.2rem;
  img {
    width: 100%;
  }
}
</style>
<style lang="scss" scoped>
header {
  position: fixed;
  width: 100%;
  z-index: 2;
  background-color: #f9f9f9;
  > div {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    width: 100%;
    max-width: 57rem;
    margin: auto;
    padding: 0.5rem;
    box-sizing: border-box;
  }
}
main {
  width: 100%;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-evenly;
  gap: 0.5rem;
  max-width: 57rem;
  margin: 3rem auto;
  padding: 0.5rem;
  box-sizing: border-box;
}
aside {
  width: 20rem;
  height: 100vh;
  position: fixed;
  overflow: hidden;
  overflow-y: scroll;
  z-index: 1;
  top: 0;
  right: -20rem;

  background-color: #2a2a2b;
  color: #f9f9f9;

  box-shadow: 0px 2px 3px rgba(0, 0, 0, 0.3);
  transition: right 1s;

  &.open {
    right: 0;
  }
}

.card {
  width: 12rem;
  padding: 0.75rem;
  border-radius: 0.5rem;
  background-color: #1a1a1a;
  color: #f9f9f9;

  &_body {
    height: 3.5rem;
  }
  &_footer {
    text-align: right;
    .favoriteBtn {
      background-color: #f9f9f9;
      filter: sepia(0);
      &:hover {
        filter: sepia(1);
      }
    }
  }
}
</style>
