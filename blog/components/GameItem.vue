<template>
  <div class="flex content-h-170 acgn-item-body">
    <div class="flex-shrink-0 relative game-cover-body">
      <div
        class="acgn-item-cover-body relative content-h-139 flex justify-center items-center border border-solid border-gray-300 rounded-md p-1"
      >
        <WikimoeImage
          class="max-image rounded game-cover"
          :class="{ 'movie-cover-none': !game.cover }"
          :src="game.cover || '/img/nopic400-565.png'"
          :alt="game.title"
          :data-href="game.cover"
          :data-href-list="game.cover ? setDataHrefList(game.cover) : null"
          loading="lazy"
          fit="contain"
        />
        <div class="absolute bottom-0 left-0 p-1" v-if="game.label?.length > 0">
          <UBadge
            v-for="(label, index) in game.label"
            :key="index"
            size="xs"
            class="mr-1"
          >
            {{ label }}
          </UBadge>
        </div>
      </div>
      <Rating :rating="game.rating" />
    </div>
    <div class="acgn-right-content content-h-170 custom-scroll">
      <div class="w-full flex flex-col">
        <div class="font-bold mb-1 flex-shrink-0">
          <span
            class="games-platform-block"
            :style="{
              backgroundColor: game.gamePlatform.color,
            }"
            v-if="game.gamePlatform"
            >{{ game.gamePlatform.name }}</span
          >{{ game.title }}
        </div>
        <div
          class="text-sm mb-1 text-gray-400 flex-shrink-0 w_10 flex items-center"
          v-if="game.giveUp"
        >
          <div v-if="game.startTime && game.endTime">
            <div class="acgn-time text-gray-400">
              {{
                `${formatDate(
                  game.startTime,
                  'yyyy年M月dd日 h时'
                )} ~ ${formatDate(game.endTime, 'yyyy年M月dd日 h时')}`
              }}
            </div>
            <div
              class="text-sm mb-1 text-gray-400 flex-shrink-0 w_10 flex items-center"
            >
              <UIcon
                name="i-heroicons-bookmark-slash"
                class="align-middle acgn-time-icon"
              />游玩{{ getACGDuration(game.startTime, game.endTime) }}后弃坑
            </div>
          </div>
          <template v-else>
            <UIcon
              name="i-heroicons-bookmark-slash"
              class="align-middle acgn-time-icon"
            />已弃坑
          </template>
        </div>
        <!-- 用时 -->
        <div v-else-if="game.startTime">
          <div class="acgn-time text-gray-400">
            {{
              `${formatDate(game.startTime, 'yyyy年M月dd日 h时')} ~ ${
                game.endTime
                  ? formatDate(game.endTime, 'yyyy年M月dd日 h时')
                  : '攻略中'
              }`
            }}<LoadingDots v-if="!game.endTime && showAnimeDot" />
          </div>
          <div
            class="text-sm mb-1 text-gray-400 flex-shrink-0 w_10 flex items-center"
          >
            <template v-if="!game.endTime"
              ><UIcon
                name="i-heroicons-clock"
                class="align-middle acgn-time-icon"
              />已累计游玩</template
            ><template v-else
              ><UIcon
                name="i-heroicons-star"
                class="align-middle acgn-time-icon"
              />共计游玩</template
            >{{ getACGDuration(game.startTime, game.endTime) }}
          </div>
        </div>
        <!-- 链接 -->
        <div
          class="text-sm mb-1 text-gray-500 flex-shrink-0"
          v-if="game.urlList.length > 0 || game.screenshotAlbum"
        >
          <a
            :href="url.url"
            target="_blank"
            class="inline-flex items-center text-primary mr-2"
            v-for="(url, index) in game.urlList"
            :key="index"
          >
            <UIcon name="i-heroicons-link" class="align-middle mr-1" />
            {{ url.text }}
          </a>
          <a
            href="javascript:;"
            class="inline-flex items-center text-primary mr-2"
            v-if="game.screenshotAlbum"
            @click="showAlbum(game.screenshotAlbum._id)"
          >
            <UIcon name="i-heroicons-photo" class="align-middle mr-1" />
            相关截图
          </a>
        </div>

        <div class="acg-summary">
          <!-- prettier-ignore -->
          <div class="text-sm whitespace-pre-line text-gray-500 flex-grow dark:text-gray-300" v-if="game.summary">{{ game.summary }}</div>
          <div v-else class="text-sm text-gray-400">暂无内容</div>
        </div>
      </div>
    </div>
  </div>
  <ClientOnly>
    <AlbumPhotoSwipe ref="AlbumPhotoSwipeRef" :albumId="activeAlbumId" />
  </ClientOnly>
</template>
<script setup>
const props = defineProps({
  game: {
    type: Object,
    required: true,
  },
  showAnimeDot: {
    type: Boolean,
    default: true,
  },
})

const setDataHrefList = (cover) => {
  return [
    {
      filepath: cover,
    },
  ]
}

// 相册
const activeAlbumId = ref('')
const AlbumPhotoSwipeRef = ref(null)

const showAlbum = (id) => {
  activeAlbumId.value = id
  nextTick(() => {
    AlbumPhotoSwipeRef.value.open()
  })
}
</script>
<style scoped>
.game-cover-body {
  width: 100px;
}
.games-platform-block {
  color: #fff;
  padding: 2px 5px;
  border-radius: 2px;
  font-size: 12px;
  margin-right: 5px;
  border-radius: 5px;
  display: inline-block;
  line-height: 18px;
}
</style>
