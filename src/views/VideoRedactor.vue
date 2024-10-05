<template>
  <main>
    <aside>
      <ul>
        <li>d</li>
        <li>t</li>
        <li>w</li>
        <li>a</li>
      </ul>
    </aside>
    <div
      class="video_container"
      @dragover.prevent="onDragOver"
      @drop="onDrop"
    >
      <div class="video_container__video"></div>
    </div>
    <aside>
      <div>
        <label for="add_videos">Add clip</label>
        <input
          type="file"
          accept="video/*"
          @change="onFileChange"
          id="add_videos"
        />
      </div>
      <ul>
        <li
          class="clip"
          v-for="(src, index) in videoSrcs"
          :key="index"
          draggable="true"
          @dragstart="onDragStart"
        >
          {{ VideoNames[index] }}
        </li>
      </ul>
    </aside>
  </main>
</template>

<script>
import { ref, onBeforeUnmount, onMounted } from 'vue';
import { FFmpeg } from '@ffmpeg/ffmpeg';
import { fetchFile, toBlobURL } from '@ffmpeg/util'

export default {
  setup() {
    const ffmpeg = new FFmpeg();
    const load = async () => {
      await ffmpeg.load( );
    };

    const videoSrcs = ref([]);
    const VideoNames = ref([]);

    const onFileChange = async (event) => {
      const files = event.target.files;
      for (let i = 0; i < files.length; i++) {
        const file = files[i];
        VideoNames.value.push(file.name);
        videoSrcs.value.push(URL.createObjectURL(file));
        const fileData = fetchFile(URL.createObjectURL(file)); 
        console.log(fileData)
      }
    };

    const onDragStart = () => {
      console.log("start");
    };

    const onDragOver = (event) => {
      event.preventDefault();
    };

    const onDrop = () => {
      console.log("drop");
    };

    onMounted(() => {
      console.log("loading");
      load();
      console.log("loaded");
    });

    onBeforeUnmount(() => {
      videoSrcs.value.forEach((src) => URL.revokeObjectURL(src));
      // No need to revoke lastVideo as it's an internal FFmpeg object
    });

    return {
      videoSrcs,
      VideoNames,
      onFileChange,
      onDragStart,
      onDragOver,
      onDrop
    };
  }
}
</script>


<style lang="sass" scoped>
$grey: rgb(200, 200, 200)
main
  display: flex
aside
  flex: 1
li
  list-style-type: none
ul
  padding: 0
input[type="file"]
  display: none
label
  display: inline-block
  cursor: pointer
.video_container
  margin: auto
  flex: 8
  height: 80vh
  width: 80%
  &__video
    background-color: $grey
    height: 70vh 
.clip
  margin: 2px
  background-color: $grey
</style>
