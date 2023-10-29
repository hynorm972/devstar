<script>
	import { onMount, afterUpdate } from 'svelte';
	import QrCodeReader from 'qrcode-reader';
  
	let videoElement;
	let qrScanner;
	let qrData = '';
  
	onMount(() => {
	  initQRScanner();
	});
  
	function initQRScanner() {
	  const constraints = {
		video: { facingMode: 'environment' },
	  };
  
	  navigator.mediaDevices
		.getUserMedia(constraints)
		.then((stream) => {
		  videoElement.srcObject = stream;
		  qrScanner = new QrCodeReader();
		  qrScanner.init();
  
		  const canvas = document.createElement('canvas');
		  const context = canvas.getContext('2d');
  
		  const scanQRCode = () => {
			if (videoElement.readyState === videoElement.HAVE_ENOUGH_DATA) {
			  canvas.width = videoElement.videoWidth;
			  canvas.height = videoElement.videoHeight;
			  context.drawImage(videoElement, 0, 0, canvas.width, canvas.height);
			  qrScanner.decode(context.getImageData(0, 0, canvas.width, canvas.height));
			}
			requestAnimationFrame(scanQRCode);
		  };
  
		  qrScanner.scanQrCode = (result) => {
			qrData = result;
		  };
  
		  scanQRCode();
		})
		.catch((error) => {
		  console.error('Error accessing the camera:', error);
		});
	}
  
	afterUpdate(() => {
	  if (qrData) {
		
		console.log('QR Code Data:', qrData);
		qrData = '';
	  }
	});
  </script>
  
  <style>
	video {
	  width: 100%;
	  max-width: 640px;
	}
  </style>
  
  <div>
	<video bind:this={videoElement} autoplay></video>
  </div>