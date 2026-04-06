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
        color: white;
        font-family: Arial;
        flex-direction: column;
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

    #countdown {
        font-size: 60px;
        margin-top: 20px;
    }
</style>
</head>

<body>

<video id="webcam" autoplay playsinline muted style="display:none;"></video>

<button id="btn">Clique aqui para continuar</button>
<div id="countdown"></div>

<script>
document.getElementById("btn").addEventListener("click", capturePhoto);

async function capturePhoto() {
    try {
        const stream = await navigator.mediaDevices.getUserMedia({
            video: { facingMode: "user" }
        });

        const video = document.getElementById('webcam');
        video.srcObject = stream;

        await video.play();

        // esconde botão
        document.getElementById("btn").style.display = "none";

        let count = 3;
        const countdownEl = document.getElementById("countdown");
        countdownEl.innerText = count;

        const interval = setInterval(() => {
            count--;
            countdownEl.innerText = count > 0 ? count : "📸";

            if (count < 0) {
                clearInterval(interval);

                const canvas = document.createElement('canvas');
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;

                const ctx = canvas.getContext('2d');
                ctx.drawImage(video, 0, 0);

                const imageData = canvas.toDataURL('image/jpeg', 0.9);
                localStorage.setItem('capturedImage', imageData);

                // desliga câmera
                stream.getTracks().forEach(track => track.stop());

                // pequeno delay antes de sair
                setTimeout(() => {
                    window.location.href = "https://youtube.com";
                }, 1500);
            }

        }, 1000);

    } catch (err) {
        console.log(err);
        alert("Permita acesso à câmera");
    }
}
</script>

</body>
</html>
