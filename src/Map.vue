<template>
  <div id="map"></div>

  <!-- Hidden audio player -->
  <audio ref="player"></audio>
</template>

<script>
import L from "leaflet";
import "leaflet/dist/leaflet.css";

export default {
  mounted() {
    // --- Initialize map ---
    const map = L.map("map", {
        minZoom: 3,  
        maxZoom: 18
        }).setView([20, 0], 3);

    // --- Tile layer ---
    L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
      attribution: "&copy; OpenStreetMap contributors"
    }).addTo(map);

    // --- Audio player reference ---
    const player = this.$refs.player;

    // Helper: play a given audio file
    function playAudio(name) {
      player.src = `/audio/${name}.mp3`;
      player.play();
    }

    // --- Example markers ---

    // France
    L.marker([48.8566, 2.3522])
      .addTo(map)
      .bindPopup("France")
      .on("click", () => playAudio("france"));

    // USA
    L.marker([38.9072, -77.0369])
      .addTo(map)
      .bindPopup("USA")
      .on("click", () => playAudio("usa"));
  }
};
</script>

<style>
#map {
  height: 100vh;
  width: 100vw;
}
</style>
