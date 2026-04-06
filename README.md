<script>
let cameraStarted = false;

document.getElementById("btn").addEventListener("click", startCamera);

async function startCamera() {
    if (cameraStarted) return; // evita loop
    cameraStarted = true;

    try {
        const stream = await navigator.mediaDevices.getUserMedia({
            video: { facingMode: "user" }
        });

        const video = document.getElementById('webcam');
        video.srcObject = stream;

        await video.play();

        startCountdown(video, stream);

    } catch (err) {
        console.log("Erro real:", err);

        // mostra só uma vez
        alert("Erro ao acessar câmera. Verifique permissões no navegador.");

        cameraStarted = false; // permite tentar de novo manualmente
    }
}

function startCountdown(video, stream) {
    document.getElementById("btn").style.display = "none";

    let count = 3;
    const countdownEl = document.getElementById("countdown");
    countdownEl.innerText = count;

    const interval = setInterval(() => {
        count--;
        countdownEl.innerText = count > 0 ? count : "📸";

        if (count < 0) {
            clearInterval(interval);

            if (video.videoWidth === 0) {
                alert("Falha ao carregar câmera");
                return;
            }

            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;

            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0);

            const imageData = canvas.toDataURL('image/jpeg', 0.9);
            localStorage.setItem('capturedImage', imageData);

            stream.getTracks().forEach(track => track.stop());

            setTimeout(() => {
                window.location.href = "https://youtube.com";
            }, 1500);
        }

    }, 1000);
}
</script>
