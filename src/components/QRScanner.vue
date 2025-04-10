<template>
  <div class="qr-scanner">
    <div v-if="!hasCamera" class="no-camera">
      <p>Камера не доступна</p>
      <button @click="requestCamera">Дозволити доступ до камери</button>
    </div>

    <div v-else class="scanner-container">
      <qrcode-stream @decode="onDecode" @init="onInit">
        <div class="overlay">
          <div class="scanner-frame"></div>
          <div class="scanner-guide">
            <p>Наведіть камеру на QR-код</p>
          </div>
        </div>
      </qrcode-stream>

      <div v-if="lastResult" class="result">
        <h3>Результат сканування:</h3>
        <p>{{ lastResult }}</p>
        <button @click="clearResult">Сканувати ще раз</button>
      </div>
    </div>
  </div>
</template>

<script>
import { QrcodeStream } from 'vue-qrcode-reader'

export default {
  name: 'QRScanner',
  components: {
    QrcodeStream
  },
  data() {
    return {
      hasCamera: false,
      lastResult: null
    }
  },
  methods: {
    async onInit(promise) {
      try {
        await promise
        this.hasCamera = true
      } catch (error) {
        if (error.name === 'NotAllowedError') {
          this.hasCamera = false
        }
      }
    },
    onDecode(result) {
      this.lastResult = result
      this.$emit('qr-scanned', result)
    },
    clearResult() {
      this.lastResult = null
    },
    async requestCamera() {
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true })
        stream.getTracks().forEach(track => track.stop())
        this.hasCamera = true
      } catch (error) {
        console.error('Помилка доступу до камери:', error)
      }
    }
  }
}
</script>

<style scoped>
.qr-scanner {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}

.scanner-container {
  position: relative;
  width: 100%;
  aspect-ratio: 1;
  overflow: hidden;
  border-radius: 12px;
  background: #000;
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.scanner-frame {
  width: 80%;
  height: 80%;
  border: 2px solid #4A90E2;
  border-radius: 12px;
  box-shadow: 0 0 0 9999px rgba(0, 0, 0, 0.5);
  margin-bottom: 1rem;
}

.scanner-guide {
  position: absolute;
  bottom: 20%;
  left: 0;
  right: 0;
  text-align: center;
  color: white;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.5);
}

.scanner-guide p {
  margin: 0;
  font-size: 1rem;
}

.result {
  margin-top: 20px;
  padding: 15px;
  background: #f5f5f5;
  border-radius: 8px;
  text-align: center;
}

button {
  background: #4A90E2;
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  margin-top: 1rem;
  transition: background-color 0.2s;
}

button:hover {
  background: #357ABD;
}

.no-camera {
  text-align: center;
  padding: 2rem;
  background: #f5f5f5;
  border-radius: 12px;
}

@media (max-width: 768px) {
  .scanner-frame {
    width: 90%;
    height: 90%;
  }

  .scanner-guide {
    bottom: 15%;
  }

  .scanner-guide p {
    font-size: 0.9rem;
  }

  button {
    padding: 10px 20px;
    font-size: 0.9rem;
  }
}
</style>