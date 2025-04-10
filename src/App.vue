<script setup>
import { ref } from 'vue'
import QRScanner from './components/QRScanner.vue'

const scannedData = ref(null)
const scanHistory = ref([])

const handleQRScanned = (result) => {
  console.log('Відскановано QR-код:', result)
  scannedData.value = result
  scanHistory.value.unshift({
    data: result,
    timestamp: new Date().toLocaleTimeString()
  })
}
</script>

<template>
  <div class="app">
    <header>
      <h1>Pet QR Scanner</h1>
    </header>

    <main>
      <div class="scanner-section">
        <QRScanner @qr-scanned="handleQRScanned" />
      </div>

      <div class="results-section" v-if="scannedData">
        <div class="current-scan">
          <h2>Останнє сканування</h2>
          <div class="scan-data">
            <p>{{ scannedData }}</p>
          </div>
        </div>

        <div class="scan-history" v-if="scanHistory.length > 0">
          <h2>Історія сканувань</h2>
          <div class="history-list">
            <div v-for="(scan, index) in scanHistory" :key="index" class="history-item">
              <span class="time">{{ scan.timestamp }}</span>
              <p class="data">{{ scan.data }}</p>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<style>
.app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
}

header {
  margin-bottom: 2rem;
}

h1 {
  color: #4A90E2;
  font-size: 2rem;
  margin: 0;
}

main {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  align-items: center;
  min-height: 60vh;
}

.scanner-section {
  width: 100%;
  max-width: 600px;
}

.results-section {
  width: 100%;
  max-width: 600px;
  margin-top: 2rem;
}

.current-scan, .scan-history {
  background: #f5f5f5;
  border-radius: 12px;
  padding: 1.5rem;
  margin-bottom: 1.5rem;
}

h2 {
  color: #333;
  font-size: 1.5rem;
  margin: 0 0 1rem 0;
}

.scan-data {
  background: white;
  padding: 1rem;
  border-radius: 8px;
  word-break: break-all;
}

.history-list {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.history-item {
  background: white;
  padding: 1rem;
  border-radius: 8px;
  text-align: left;
}

.time {
  color: #666;
  font-size: 0.9rem;
  display: block;
  margin-bottom: 0.5rem;
}

.data {
  margin: 0;
  word-break: break-all;
}

@media (max-width: 768px) {
  .app {
    padding: 10px;
  }

  h1 {
    font-size: 1.5rem;
  }

  .current-scan, .scan-history {
    padding: 1rem;
  }

  h2 {
    font-size: 1.2rem;
  }
}
</style>
