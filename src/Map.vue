<template>
  <div id="map"></div>
  <audio ref="player" controls></audio>
</template>

<script>
import L from "leaflet";
import "leaflet/dist/leaflet.css";
import { capitals } from "./capitals.js";

export default {
  async mounted() {
    const map = L.map("map", {
      minZoom: 2,
      maxZoom: 18
    }).setView([20, 0], 2);

    L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
      attribution: "&copy; OpenStreetMap contributors"
    }).addTo(map);

    const player = this.$refs.player;

    function playAudio(name) {
      player.src = `/audio/${name}.mp3`;
      player.play();
    }

    // Helper to check if audio exists
    async function audioExists(name) {
      try {
        const response = await fetch(`/audio/${name}.mp3`, { method: "HEAD" });
        return response.ok;
      } catch (err) {
        return false;
      }
    }

    // Filter capitals with available audio
    const capitalsWithAudio = await Promise.all(
      capitals.map(async cap => {
        const audioName = cap.city.toLowerCase().replace(/\s+/g, "__").replace(/[^a-z0-9_]/g, "");
        if (await audioExists(audioName)) {
          return { ...cap, audioName };
        }
        return null;
      })
    );

    // Add markers only for cities with audio
    capitalsWithAudio
      .filter(cap => cap !== null)
      .forEach(cap => {
        L.marker([cap.lat, cap.lon])
          .addTo(map)
          .bindPopup(`${cap.city}, ${cap.country}`)
          .on("click", () => playAudio(cap.audioName));
      });
  }
};
</script>

<style>
#map {
  height: calc(100vh - 40px); /* Leave space for the audio player */
  width: 100vw;
}

audio {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 40px;
  background: #f8f8f8;
}
</style>
