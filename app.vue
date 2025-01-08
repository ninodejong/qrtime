<template>
  <div class="min-h-screen bg-gray-100">
    <div class="flex justify-center items-center">
      <div class="bg-white p-8 rounded-lg shadow-lg max-w-md w-full mt-8">
        <!-- <h1 class="text-2xl font-bold mb-6 text-center text-gray-800">
          QR Time
        </h1> -->

        <div class="flex justify-center mb-6">
          <img :src="qrcodeContainer" class="w-full" />
        </div>

        <div class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1"
              >URL Prefix</label
            >
            <input
              type="text"
              v-model="urlPrefix"
              placeholder="https://example.com/"
              class="w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-500"
            />
          </div>

          <div class="flex items-center">
            <input
              type="checkbox"
              v-model="includeTimezone"
              class="h-4 w-4 text-blue-600 border-gray-300 rounded focus:ring-blue-500"
            />
            <label class="ml-2 block text-sm text-gray-700">Timezone</label>
          </div>

          <!-- <div class="text-center text-gray-600 text-sm">
          {{ currentContent }}
        </div> -->
        </div>
      </div>
    </div>
    <p class="pt-4 text-xs text-center text-gray-500">
      <small
        >Een ongevraagde website van
        <a class="text-blue-300" href="https://ninodejong.nl/" target="_blank"
          >Nino de Jong</a
        ></small
      >
    </p>
  </div>
</template>

<script setup>
import QRCode from "qrcode";
const qrcodeContainer = ref(null);
const urlPrefix = ref("");
const includeTimezone = ref(false);
const currentContent = ref("");
let interval = null;

const generateQR = () => {
  const timestamp = new Date();
  let content = timestamp.toISOString();

  if (!includeTimezone.value) {
    // console.log(content.split("T")[1].replace("Z", ""));
    // content = content.split(".")[0].replace("T", " ");
    content = content.split("T")[1].replace("Z", "");
  }

  if (urlPrefix.value) {
    content = urlPrefix.value + content;
  }

  currentContent.value = content;
  QRCode.toDataURL(content)
    .then((url) => {
      qrcodeContainer.value = url;
    })
    .catch((err) => {
      console.error(err);
    });
};

onMounted(async () => {
  generateQR();

  interval = setInterval(generateQR, 1000);
});

onBeforeUnmount(() => {
  if (interval) {
    clearInterval(interval);
  }
});

watch([urlPrefix, includeTimezone], generateQR, { immediate: true });
</script>
