<script setup lang="ts">
import { desktopCapturer, Menu } from '@electron/remote'
import { DesktopCapturerSource } from 'electron'

async function loadSources() {
  const sources = await desktopCapturer.getSources({
    types: ['window', 'screen']
  })

  const menu = Menu.buildFromTemplate(
    sources.map((source) => ({
      label: source.name,
      click: () => sourceSelected(source)
    })
  ))

  menu.popup()
}

async function sourceSelected(source: DesktopCapturerSource) {
  const stream = await navigator.mediaDevices.getUserMedia({
    audio: false,
    video: {
      // @ts-ignore
      mandatory: {
        chromeMediaSource: 'desktop',
        chromeMediaSourceId: source.id,
        minWidth: 1280,
        maxWidth: 1280,
        minHeight: 720,
        maxHeight: 720
      }
    }
  })
  const video = document.getElementById('mainStream') as HTMLVideoElement

  video.srcObject = stream
  video.onloadedmetadata = () => video.play()
}
</script>

<template>
  <video id="mainStream" />
  <button class="px-4 py-3 bg-blue-600 rounded-md text-white outline-none focus:ring-4 shadow-lg transform active:scale-x-75 transition-transform mx-5 flex" @click="loadSources">
    Select source
  </button>
</template>
