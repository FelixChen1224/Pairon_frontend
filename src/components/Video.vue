<template>
  <div id="app" data-v-app style="padding: 0">
    <div class="main-container">
      <div class="container">
        <!-- <div class="video-wrapper">
          <canvas ref="canvasElement"></canvas>
          <p>處理中的畫面</p>
        </div> -->

        <div class="video-section">
          <div class="video-col">
            <media-player src="http://127.0.0.1:5001/video/output.mp4">
              <media-provider></media-provider>
              <media-video-layout
                thumbnails="https://files.vidstack.io/sprite-fight/thumbnails.vtt"
              ></media-video-layout>
            </media-player>
          </div>
          <BProgress :max="total" class="w-100 mb-2">
            <BProgressBar :value="time" :label="`${time} / ${total} `"></BProgressBar>
          </BProgress>
          <br />
          <span>已過/預估完成時間 : {{ elapsed }} / {{ remaining }} </span>
          <br />
          <br />
          <input type="file" @change="handleFileChange" accept="video/*,image/*" />
          <br />
          <br />
          <div class="controls">
            <button @click="uploadFile">Upload video</button>
            <button @click="uploadFile">Image upload</button>
            <button @click="example">Run example</button>
          </div>
        </div>
      </div>

      <div class="track-info-container">
        <!-- 添加條件渲染 -->
        <div class="track-cards" v-if="info?.results?.track_info">
          <div
            v-for="[trackId, data] in Object.entries(info.results.track_info)"
            :key="trackId"
            class="track-card"
          >
            <div class="card-header">
              <h3>ID: {{ trackId }} - {{ data.name }}</h3>
            </div>

            <div class="card-body">
              <div class="time-info">
                <p>
                  出現時間:
                  <span @click="setPlayerTime(data.appear)" class="time-link">
                    {{ data.appear }}
                  </span>
                </p>
                <p>
                  消失時間:
                  <span
                    v-if="data.disappear"
                    @click="setPlayerTime(data.disappear)"
                    class="time-link"
                  >
                    {{ data.disappear }}
                  </span>
                  <span v-else>尚未消失</span>
                </p>
              </div>

              <div class="image-container">
                <img
                  :src="data.appear_image_path"
                  v-if="data.appear_image_path"
                  :alt="`Track ${trackId} 影像`"
                  class="track-image"
                />
              </div>

              <!-- 主要特徵顯示 -->
              <div class="features-summary" v-if="data.features">
                <h4>主要特徵：</h4>
                <ul>
                  <li v-if="hasFeature(data, 'Upper-clothes')">
                    {{ data.features['Upper-clothes'][0].description }}
                    ({{ data.features['Upper-clothes'][0].confidence.toFixed(2) }}%)
                  </li>
                  <li v-if="hasFeature(data, 'Face')">
                    {{ data.features['Face'][0].description }}
                    ({{ data.features['Face'][0].confidence.toFixed(2) }}%)
                  </li>
                  <li v-if="hasFeature(data, 'Bag')">
                    {{ data.features['Bag'][0].description }}
                    ({{ data.features['Bag'][0].confidence.toFixed(2) }}%)
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="grid-section">
        <!-- <div class="input-button-container">
          <input v-model="searchQuery" placeholder="搜尋 Track ID 或 Name" />
          <button @click="search">搜尋</button>
        </div> -->

        <div class="Ditem-container-2">
          <div
            v-for="item in layout.filter((item) => item.itemKey % 2 === 0)"
            :key="item.itemKey"
            class="Ditem-container"
          >
            <div>
              <p class="track-id">Track ID: {{ item.itemKey }} - {{ item.name }}</p>
              <div class="Ditem-time">
                <p>
                  出現時間:
                  <a href="#" @click.prevent="setPlayerTime(item.timeline.first_appearance)">{{
                    item.timeline.first_appearance
                  }}</a>
                </p>
                <p>
                  消失時間:
                  <a
                    v-if="item.timeline.last_appearance"
                    href="#"
                    @click.prevent="setPlayerTime(item.timeline.last_appearance)"
                    >{{ item.timeline.last_appearance }}</a
                  >
                  <span v-else>尚未消失</span>
                </p>
              </div>

              <div class="Dimage">
                <img
                  v-if="item.itemKey == 2"
                  src="@/assets/temp/frame_103.jpg"
                  alt="描述圖片的替代文字"
                />
                <img
                  v-if="item.itemKey == 4"
                  src="@/assets/temp/frame_129.jpg"
                  alt="描述圖片的替代文字"
                />
              </div>

              <div class="Ditem-footer">
                <!-- 这里根据你的数据结构找到最相似特征的图片路径 -->
                <!-- 假设 item.features.text_analysis[0].features 里有相关特征信息 -->
                <div
                  v-if="
                    item.features &&
                    item.features.text_analysis &&
                    item.features.text_analysis.length > 0
                  "
                >
                  <div class="Ditem-frame">
                    特徵偵測 (Frame {{ item.features.text_analysis[0].frame }}):
                  </div>
                  <div class="Ditem-result">
                    <span v-if="item.itemKey == 4">pack bag (34.24%) <br /></span>

                    <span v-if="item.itemKey == 4">male (32.26%)</span>
                    <span v-if="item.itemKey == 2">hoodie upper-clothes (36.29%)</span>
                    <br />

                    <span v-if="item.itemKey == 2">shoulder bag (31.95%)</span>
                  </div>
                  <!-- <ul>
                    <li
                      v-for="(feature, name) in item.features.text_analysis[0].features"
                      :key="name"
                    >
                      <strong>{{ name }}:</strong>
                      <ul>
                        <li v-for="subFeature in feature" :key="subFeature.description">
                          {{ subFeature.description }} ({{ subFeature.confidence.toFixed(2) }}%)
                        </li>
                      </ul>
                    </li>
                  </ul> -->
                </div>
                <div v-else>
                  <p>沒有可用的特徵</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import io from 'socket.io-client'
import axios from 'axios'
import 'vidstack/bundle'
import { GridLayout, GridItem } from 'vue3-grid-layout'

const track_data = ref()
const layout = ref([])
//   [{ x: 0, y: 5, w: 2, h: 5, i: '6', static: false },
//   { x: 2, y: 5, w: 2, h: 5, i: '7', static: false },
//   { x: 2, y: 5, w: 2, h: 5, i: '7', static: false },
//   { x: 2, y: 5, w: 2, h: 5, i: '7', static: false },
//   { x: 0, y: 10, w: 2, h: 5, i: '12', static: false },
//   { x: 4, y: 8, w: 2, h: 5, i: '14', static: false },
//   { x: 4, y: 5, w: 2, h: 5, i: '8', static: false },
//   { x: 0, y: 9, w: 2, h: 5, i: '18', static: false }]
const file = ref(null)
const fileType = ref(null)
const searchQuery = ref('')
const time = ref(0)
const total = ref(0)
const elapsed = ref(0)
const remaining = ref(0)
const socket = ref(null)
const colNum = ref(10)
const captureInterval = ref(null)
const info = ref(null)
onMounted(() => {
  fetch('track_info.json')
    .then((response) => {
      if (!response.ok) {
        throw new Error('網絡錯誤')
      }
      return response.json()
    })
    .then((data) => {
      track_data.value = data.tracks // 假设你的 JSON 结构是 { tracks: { ... } }
      addItems()
      console.log('讀取的 JSON 數據:', data)
    })
    .catch((error) => {
      console.error('讀取 JSON 失敗:', error)
    })
})

let index = 0

// 添加一個輔助函數來檢查特徵是否存在
const hasFeature = (data, featureType) => {
  return data.features?.[featureType]?.[0]?.description != null
}

const search = () => {
  const query = searchQuery.value
  axios
    .get('http://127.0.0.1:5001/query', {
      params: {
        name: query
      }
    })
    .then((response) => {
      const results = response.data
      layout.value = results.map((item, index) => ({
        x: (index * 2) % (colNum.value || 10),
        y: 0,
        w: 2,
        h: 7,
        i: index.toString(),
        itemKey: item.track_id,
        ...item
      }))
    })
    .catch((error) => {
      console.error('Search failed:', error)
    })
}

const addItems = () => {
  layout.value = [] // 清空 layout
  index = 0 // 重置 index
  Object.entries(track_data.value).forEach(([key, item]) => {
    layout.value.push({
      x: (index * 2) % (colNum.value || 10),
      y: 0,
      w: 2,
      h: 7,
      i: index.toString(),
      itemKey: key,
      ...item
    })
    index++
  })
}

async function startStream() {
  // const canvas = this.$refs.canvasElement
  // canvas.width = this.$refs.videoElement.videoWidth || 640
  // canvas.height = this.$refs.videoElement.videoHeight || 480

  socket.value = io('http://127.0.0.1:5001')
  socket.value.on('processed_frame', (data) => {
    handleServerMessage(data)
  })
}

async function getTrackInfo() {
  try {
    socket.value = io('http://127.0.0.1:5001')
    socket.value.on('track_info', (data) => {
      console.log('Received track info:', data.track_info)
    })
  } catch (err) {
    console.error('Failed to get track info:', err)
  }
}

const setPlayerTime = (time) => {
  const player = document.querySelector('media-player')
  player.currentTime = parseFloat(time)
}

const handleFileChange = (event) => {
  file.value = event.target.files[0]
  fileType.value = file.value.type.startsWith('video/') ? 'video' : 'image'
  console.log('Selected file:', fileType.value)
}

const uploadFile = () => {
  if (file.value) {
    const formData = new FormData()
    formData.append(fileType.value, file.value)

    axios
      .post(`http://127.0.0.1:5001/upload/${fileType.value}`, formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      })
      .then((response) => {
        console.log('Upload success:', response.data)
      })
      .catch((error) => {
        console.error('Upload failed:', error)
      })
  } else {
    console.log('請先選擇一個文件')
  }
}

// const getImage = (filename) => {

//   if (filename) {

//     axios.post(`http://127.0.0.1:5001/image/${filename}`, {
//       headers: {
//         'Content-Type': 'application/json'
//       }
//     }).then((response) => {
//       console.log('請求成功:', response.data)
//       re
//     }).catch((error) => {
//       console.error('請求失敗:', error)
//     })
//   }

// }

async function getVideoURL() {
  const player = document.querySelector('media-player')
  try {
    socket.value = io('http://127.0.0.1:5001')
    socket.value.on('video_processed', (data) => {
      player.src = data.video_url
      console.log('Received video URL:', data.video_url)
    })
  } catch (err) {
    console.error('Failed to get video URL:', err)
  }
}

async function receiveImage() {
  try {
    socket.value = io('http://127.0.0.1:5001')
    socket.value.on('images_processed', (data) => {
      console.log('Received images:', data.images)
    })
  } catch (err) {
    console.error('Failed to get images:', err)
  }
}

const handleServerMessage = (data) => {
  console.log('Received processed frame from the server')
  const img = new Image()
  img.onload = () => {
    const canvas = document.querySelector('canvas')
    const ctx = canvas.getContext('2d')
    ctx.drawImage(img, 0, 0, canvas.width, canvas.height)
  }
  img.src = 'data:image/jpeg;base64,' + data.frame

  time.value = data.time
  total.value = data.total
  elapsed.value = data.elapsed
  remaining.value = data.remaining
}

async function getFrameInfo() {
  try {
    socket.value = io('http://127.0.0.1:5001')
    socket.value.on('frame_processing_results', (data) => {
      console.log('Received frame_processing_results:', data)
      info.value = data
    })
  } catch (err) {
    console.error('Failed to get track info:', err)
  }
}

async function getResults() {
  axios
    .get('http://127.0.0.1:5001/video/results')
    .then((response) => {
      console.log('Received results:', response.data)
    })

const example = () => {
  console.log('Example button clicked')
  startStream()
  getVideoURL()
  getTrackInfo()
  getFrameInfo()

  axios
    .post('http://127.0.0.1:5001/example', {
      headers: {
        'Content-Type': 'application/json'
      },
      withCredentials: false // 關閉 credentials
    })
    .then((response) => {
      console.log('請求成功:', response.data)
    })
    .catch((error) => {
      console.error('請求失敗:', error)
    })
}
</script>

<style scoped>
.input-button-container {
  display: flex; /* 使用 flexbox 來排列元素 */
  align-items: stretch; /* 使所有子元素的高度相同 */
  height: 36px;
}

input[type='text'] {
  width: 600px; /* 設置為父容器的 100% 寬度 */
  padding: 10px; /* 設置內邊距 */
  font-size: 16px; /* 設置字體大小 */
}

.item-container {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
}

.image-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.scaled-image {
  width: 105px; /* 設置固定寬度 */
  height: 120px; /* 設置固定高度 */
  display: block;
  margin: 0 auto;
}
.main-container {
  display: flex;
  flex-direction: row;
  gap: 20px; /* 添加左右两栏之间的间距 */
  width: 1024px;
  padding-top: 20px;
  margin: 0 auto;
}

.video-col {
  width: 100%;
  margin-bottom: 20px;
}

.grid-section {
  margin-top: 5px;

  .input-button-container {
    margin-bottom: 15px;
    /* margin-left: 10px; */
  }
}

.container {
  flex: 0 0 auto; /* 防止容器被拉伸 */
  width: 480px; /* 让容器宽度由内容决定 */
}

.video-section {
  padding: 0; /* 移除多余的内边距 */
  width: auto; /* 设置固定宽度 */
}
.video-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
}

.video-wrapper {
  position: relative;
  width: 640px;
  height: 480px;
}

video,
canvas {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.video-wrapper p {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  background-color: rgba(0, 0, 0, 0.5);
  color: white;
  padding: 5px 10px;
  border-radius: 5px;
}

.controls {
  display: flex;
  gap: 10px;
  margin-top: 10px;
}

button {
  padding: 8px 16px;
  font-size: 16px;
  background-color: #61065c;
  color: white;
  border: none;
  border-radius: 5px;
  min-width: 120px;
  cursor: pointer;
  align-items: center; /* 垂直居中 */
  justify-content: center; /* 水平居中 */
}

.progress-text {
  color: black;
  background-color: white;
  padding: 2px 5px;
  border-radius: 3px;
}
button:hover {
  background-color: #0056b3;
}

media-player {
  width: 100%;
  aspect-ratio: 4/3;
}

.progress-container {
  margin: 10px 0;
  width: 100%;
}

input[type='file'] {
  width: 100%;
  margin: 10px 0;
}

/* --------------------------------------- */

.Ditem-container {
  display: flex;
  flex-direction: column;
  margin-bottom: 15px;
  /* padding: 10px; */
  border: 1px solid #ccc;
  border-radius: 5px;
  width: 100%;
  height: 350px;
  overflow: auto;
}

.Ditem-container-2 {
  display: flex;
  flex-wrap: wrap;
}

.track-id {
  font-weight: bold;
  font-size: 20px;
  padding: 8px 24px 8px;
  background-color: #f2f3f4;
  border-bottom: 1.5px solid #d5d5d5;
}

.Dimage {
  width: 120px;
  height: 120px;
  margin: 0 auto;
}

.Ditem-time {
  padding-left: 24px;
  font-size: 16px;
}

.Ditem-footer {
  padding: 0 24px;
}

.Ditem-frame {
  padding-bottom: 6px;
}

.Ditem-result {
  background-color: #f2f3f4;
  padding: 6px 8px 6px;
  border-radius: 6px;
}

.track-info-container {
  padding: 20px;
}

.search-bar {
  margin-bottom: 20px;
  display: flex;
  gap: 10px;
}

.track-cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
}

.track-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
}

.card-header {
  background-color: #f5f5f5;
  padding: 10px;
  border-bottom: 1px solid #ddd;
}

.card-header h3 {
  margin: 0;
  font-size: 1.2em;
}

.card-body {
  padding: 15px;
}

.time-info {
  margin-bottom: 15px;
}

.time-link {
  color: #0066cc;
  cursor: pointer;
  text-decoration: underline;
}

.image-container {
  margin: 15px 0;
  text-align: center;
}

.track-image {
  max-width: 200px;
  max-height: 200px;
  object-fit: cover;
  border-radius: 4px;
}

.features-summary {
  background-color: #f9f9f9;
  padding: 10px;
  border-radius: 4px;
}

.features-summary h4 {
  margin: 0 0 10px 0;
}

.features-summary ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.features-summary li {
  margin-bottom: 5px;
  font-size: 0.9em;
}
</style>
