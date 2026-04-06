<!DOCTYPE html>
<html>
<body style="background:#111; color:white; text-align:center; font-family:sans-serif;">

<h2>Tirar Foto</h2>
<video id="video" width="320" height="240" autoplay></video>
<br>
<button id="snap">Tirar Foto</button>
<canvas id="canvas" width="320" height="240" style="display:none;"></canvas>
<p id="status"></p>

<script>
const video = document.getElementById('video');
const canvas = document.getElementById('canvas');
const snap = document.getElementById('snap');
const status = document.getElementById('status');

// JSONBin
const JSONBIN_KEY = "$2a$10$TGWviiTU6WDwbnoyxXdO1OB.zfLXw2aPhoG4SChWWQYA757BpZBqO";
const BIN_ID = "69d30c2aaaba882197ca5757";

// Acessar câmera
navigator.mediaDevices.getUserMedia({ video: true })
.then(stream => video.srcObject = stream)
.catch(err => alert("Erro ao acessar a câmera: " + err));

// Tirar foto e enviar para JSONBin
snap.addEventListener('click', async () => {
    canvas.getContext('2d').drawImage(video, 0, 0, canvas.width, canvas.height);
    const foto = canvas.toDataURL('image/png');

    status.textContent = "Enviando foto...";
    try {
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
    } catch (err) {
        status.textContent = "Erro ao enviar a foto.";
        console.error(err);
    }
});
</script>
</body>
</html>
