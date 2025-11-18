# Data-kelas
Kehadiran siswa
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<title>Data Diri Pro</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
    body {
        margin: 0;
        font-family: Arial, sans-serif;
        height: 100vh;
        overflow: hidden;
        background: radial-gradient(circle at top, #0a0f2d, #020617);
        animation: fadeIn 1s ease;
        color: white;
    }

    /* Parallax floating lights */
    .bg {
        position: absolute;
        width: 100%;
        height: 100%;
        overflow: hidden;
        top: 0; left: 0;
        pointer-events: none;
    }
    .light {
        width: 40px;
        height: 40px;
        background: rgba(0,150,255,0.4);
        filter: blur(30px);
        border-radius: 50%;
        position: absolute;
        animation: float 6s infinite ease-in-out;
    }
    @keyframes float {
        0% { transform: translateY(0px); }
        50% { transform: translateY(-30px); }
        100% { transform: translateY(0px); }
    }

    .card {
        position: relative;
        z-index: 2;
        width: 300px;
        padding: 25px;
        margin: auto;
        margin-top: 50px;
        background: rgba(255,255,255,0.1);
        border: 2px solid rgba(0,200,255,0.4);
        border-radius: 20px;
        backdrop-filter: blur(10px);
        box-shadow: 0 0 20px #00b7ff;
        animation: pop 0.8s ease;
        color: white;
    }
    @keyframes pop {
        from { transform: scale(0.6); opacity: 0; }
        to { transform: scale(1); opacity: 1; }
    }

    input {
        width: 100%;
        padding: 10px;
        margin-top: 12px;
        border-radius: 8px;
        border: none;
        outline: none;
    }

    button {
        width: 100%;
        padding: 12px;
        margin-top: 15px;
        background: #00aaff;
        border: none;
        color: white;
        font-size: 16px;
        border-radius: 8px;
        cursor: pointer;
        transition: 0.2s;
        box-shadow: 0 0 15px #00aaff;
    }
    button:hover {
        transform: scale(1.05);
        background: #0090dd;
    }

    .output {
        margin-top: 20px;
        padding: 15px;
        background: rgba(0,140,255,0.2);
        border-radius: 10px;
        display: none;
        animation: slide 0.5s ease;
    }

    @keyframes slide {
        from { opacity: 0; transform: translateY(20px); }
        to { opacity: 1; transform: translateY(0); }
    }

    .music-control {
        margin-top: 10px;
        text-align: center;
    }
    .music-control button {
        width: 150px;
        padding: 10px;
        background: #ffaa00;
        color: #000;
        box-shadow: 0 0 10px #ffaa00;
    }
    .music-control button:hover {
        background: #ff9900;
    }
</style>
</head>
<body>

<!-- PARALLAX LIGHTS -->
<div class="bg">
    <div class="light" style="top:20%; left:10%; animation-delay:0s;"></div>
    <div class="light" style="top:60%; left:70%; animation-delay:2s;"></div>
    <div class="light" style="top:40%; left:40%; animation-delay:1s;"></div>
</div>

<!-- FORM CARD -->
<div class="card">
    <h2 style="text-align:center;">DATA DIRI PRO</h2>

    <input id="nama" placeholder="Masukkan nama kamu">
    <input id="noAbsen" placeholder="Masukkan nomor absen" type="number">
    <input id="kelas" placeholder="Masukkan kelas">

    <button onclick="kirim()">Lanjut</button>

    <div class="output" id="output"></div>

    <!-- Music Control -->
    <div class="music-control">
        <button onclick="toggleMusic()">Pause / Play Musik</button>
    </div>
</div>

<!-- CLICK SOUND -->
<audio id="clickSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_7f0fd...mp3?filename=click-124467.mp3"></audio>

<!-- BACKGROUND MUSIC -->
<audio id="bgMusic" src="https://cdn.pixabay.com/download/audio/2023/03/15/audio_bgmusic.mp3" autoplay loop></audio>

<!-- FIREBASE -->
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/12.6.0/firebase-app.js";
  import { getDatabase, ref, push, set } from "https://www.gstatic.com/firebasejs/12.6.0/firebase-database.js";

  const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
    databaseURL: "https://YOUR_PROJECT_ID-default-rtdb.firebaseio.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID"
  };

  const app = initializeApp(firebaseConfig);
  const db = getDatabase(app);

  window.saveToFirebase = (nama, noAbsen, kelas) => {
    const dataRef = ref(db, "users/");
    const newData = push(dataRef);

    set(newData, {
      nama: nama,
      noAbsen: noAbsen,
      kelas: kelas,
      waktu: new Date().toLocaleString()
    })
    .then(() => console.log("✔ Data berhasil disimpan!"))
    .catch(err => console.error("❌ Error:", err));
  };
</script>

<script>
function playSound() {
    document.getElementById("clickSound").play();
}

function kirim() {
    playSound();

    const namaInput = document.getElementById('nama');
    const noAbsenInput = document.getElementById('noAbsen');
    const kelasInput = document.getElementById('kelas');
    const output = document.getElementById("output");

    const nama = namaInput.value.trim();
    const noAbsen = noAbsenInput.value.trim();
    const kelas = kelasInput.value.trim();

    if (!nama || !noAbsen || !kelas) {
        alert("⚠ Semua harus diisi!");
        return;
    }

    output.style.display = "block";
    output.innerHTML = `
        <b>Nama:</b> ${nama}<br>
        <b>No Absen:</b> ${noAbsen}<br>
        <b>Kelas:</b> ${kelas}
    `;

    if (typeof saveToFirebase === "function") {
        saveToFirebase(nama, noAbsen, kelas);
    }
}

// Optional: atur volume bgMusic
const bgMusic = document.getElementById('bgMusic');
bgMusic.volume = 0.2;

// Tombol pause/play musik
function toggleMusic() {
    if (bgMusic.paused) {
        bgMusic.play();
    } else {
        bgMusic.pause();
    }
}
</script>

</body>
</html>
