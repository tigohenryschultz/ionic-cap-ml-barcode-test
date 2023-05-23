<template>
  <ion-page>
    <ion-content id="main">
      <ion-button @click=scan>
        SCAN MULTIPLE
      </ion-button>
      <ion-button @click=scanFromImage>
        SCAN MULTIPLE
      </ion-button>
      <ion-button @click="takePicture">
        TAKE PICTURE
      </ion-button>
    </ion-content>
  </ion-page>
</template>

<script>
import {
  BarcodeScanner,
  BarcodeFormat,
  LensFacing,
} from '@capacitor-mlkit/barcode-scanning';
import {
  IonContent,
  IonPage,
  IonButton,
} from '@ionic/vue'
import { Camera, CameraResultType } from '@capacitor/camera';

export default {
  name: 'TgMLKitBarcodes',

  components: {
    IonContent,
    IonPage,
    IonButton,
  },

  data() {
    return {
      defaultCameraOptions: {
        quality: 100,
        correctOrientation: true,
        // saveToGallery: true,
        // resultType: CameraResultType.DataUrl
        resultType: CameraResultType.Uri,
      },
    }
  },

  methods: {
    takePicture: function (customCameraOptions = {}) {
      if (typeof Camera === 'undefined' || typeof Camera.getPhoto !== 'function') {
        return;
      }

      const cameraOption = {
        ...this.defaultCameraOptions,
        ...customCameraOptions,
      }

      Camera.getPhoto(cameraOption)
        .then(imageObject => {
          console.log('image:', imageObject);
          this.scanFromImage(imageObject.path)
        })
        .catch((error) => {
          console.error('TgCamera:error:', error);
        });
    },

    scanFromImage: async function (imagePath) {
      const { barcodes } = await BarcodeScanner.readBarcodesFromImage({
        formats: [BarcodeFormat.QrCode, BarcodeFormat.Code128],
        path: imagePath,
      });
      console.log(barcodes);
      return barcodes;
    },

    scan: async function () {
      const { barcodes } = await BarcodeScanner.scan({
        formats: [BarcodeFormat.QrCode, BarcodeFormat.Code128],
        lensFacing: LensFacing.Back,
      });
      console.log(barcodes);
      return barcodes;
    },


  },
}
</script>

<style lang="css" scoped>
  body.barcode-scanner-active {
    visibility: hidden;
    --background: transparent;
    --ion-background-color: transparent;
  }

  .barcode-scanner-modal {
    visibility: visible;
  }

  @media (prefers-color-scheme: dark) {
    .barcode-scanner-modal {
      --background: transparent;
      --ion-background-color: transparent;
    }
  }
</style>
