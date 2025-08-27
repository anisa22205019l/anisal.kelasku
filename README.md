# anisal.kelasku

<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Coding Puzzle Game</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f0f7ff;
      text-align: center;
      margin: 0;
      padding: 0;
    }
    h1 {
      background: #0077cc;
      color: white;
      padding: 20px;
      margin: 0;
    }
    #menu, #gameArea {
      margin-top: 30px;
    }
    button {
      padding: 12px 20px;
      margin: 10px;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      background: #0077cc;
      color: white;
      cursor: pointer;
      transition: 0.3s;
    }
    button:hover {
      background: #005fa3;
    }
    #infoBox, #devBox {
      display: none;
      background: #fff3cd;
      border: 2px solid #ffeeba;
      padding: 15px;
      margin: 20px auto;
      width: 70%;
      border-radius: 10px;
      text-align: left;
    }
    #devBox {
      background: #e6f7ff;
      border: 2px solid #91d5ff;
    }
    #pieces {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 15px;
      margin: 20px auto;
      width: 80%;
    }
    .piece {
      padding: 10px;
      width: 180px;
      background: #ffe599;
      border: 2px dashed #555;
      border-radius: 10px;
      cursor: grab;
      font-weight: bold;
    }
    #slots {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 15px;
      margin: 20px auto;
      width: 80%;
    }
    .slot {
      width: 180px;
      height: 50px;
      border: 2px dashed #aaa;
      border-radius: 10px;
      background: #fff;
    }
    #feedback {
      font-size: 20px;
      margin-top: 15px;
    }
    #stars {
      margin-top: 10px;
      font-size: 24px;
      color: gold;
    }
  </style>
</head>
<body>

  <h1>💻 Coding Puzzle Game</h1>

  <!-- Menu Utama -->
  <div id="menu">
    <button onclick="startGame()">Mulai Game</button>
    <button onclick="showInfo()">Petunjuk</button>
    <button onclick="showDev()">Developer</button>
  </div>

  <!-- Petunjuk -->
  <div id="infoBox">
    <h2>📌 Petunjuk Cara Bermain</h2>
    <p>1. Seret potongan kode ke slot kosong sesuai urutan yang benar.</p>
    <p>2. Jika urutan benar → lanjut ke level berikutnya dan mendapat <b>1 bintang ⭐</b>.</p>
    <p>3. Jika salah → <b>Game Over ❌</b> lalu coba lagi.</p>
    <p>4. Gunakan bantuan (tebakan huruf pertama) jika perlu.</p>
    <p>5. Kumpulkan bintang sebanyak-banyaknya!</p>
    <button onclick="hideInfo()">Tutup</button>
  </div>

  <!-- Developer Info -->
  <div id="devBox">
    <h2>👨‍💻 Developer</h2>
    <p>Pengembang : <b>Anisa Lidiya Chasanah</b></p>
    <p>Tahun : <b>2025</b></p>
    <p>Guru TKJ SMK BHAKTI PRAJA ADIWERNA</p>
    <button onclick="hideDev()">Tutup</button>
  </div>

  <!-- Area Game -->
  <div id="gameArea" style="display:none;">
    <h2 id="levelTitle">Level 1</h2>
    <div id="pieces"></div>

    <h3>Taruh kode di bawah ini 👇</h3>
    <div id="slots"></div>

    <div id="feedback"></div>
    <div id="stars">⭐: <span id="starCount">0</span></div>
    <button onclick="checkAnswer()">Cek Jawaban</button>
    <button onclick="giveHint()">Bantuan</button>
  </div>

  <script>
    let starCount = 0;
    let currentLevel = 0;

    // Daftar level game
    const levels = [
      {
        title: "Level 1: Program Sederhana",
        pieces: [
          {id: "p2", text: "Start"},
          {id: "p4", text: "Input Nama"},
          {id: "p1", text: 'Print("Hello World")'},
          {id: "p3", text: "End"},
        ],
        order: ["p2", "p4", "p1", "p3"],
        hint: "Kode pertama dimulai huruf 'S' (Start)"
      },
      {
        title: "Level 2: Percabangan IF",
        pieces: [
          {id: "l2p1", text: "Start"},
          {id: "l2p2", text: "Input Nilai"},
          {id: "l2p3", text: "If Nilai >= 75"},
          {id: "l2p4", text: "Print Lulus"},
          {id: "l2p5", text: "Else Print Tidak Lulus"},
          {id: "l2p6", text: "End"},
        ],
        order: ["l2p1","l2p2","l2p3","l2p4","l2p5","l2p6"],
        hint: "Pertama selalu 'Start'"
      },
      {
        title: "Level 3: Perulangan FOR",
        pieces: [
          {id: "l3p1", text: "Start"},
          {id: "l3p2", text: "For i=1 to 5"},
          {id: "l3p3", text: "Print i"},
          {id: "l3p4", text: "Next i"},
          {id: "l3p5", text: "End"},
        ],
        order: ["l3p1","l3p2","l3p3","l3p4","l3p5"],
        hint: "Awali dengan 'Start'"
      }
    ];

    // Tampilkan Game
    function startGame() {
      currentLevel = 0;
      starCount = 0;
      document.getElementById("gameArea").style.display = "block";
      document.getElementById("menu").style.display = "none";
      loadLevel();
    }

    // Petunjuk
    function showInfo() { document.getElementById("infoBox").style.display = "block"; }
    function hideInfo() { document.getElementById("infoBox").style.display = "none"; }
    function showDev() { document.getElementById("devBox").style.display = "block"; }
    function hideDev() { document.getElementById("devBox").style.display = "none"; }

    // Load level
    function loadLevel() {
      const level = levels[currentLevel];
      document.getElementById("levelTitle").innerText = level.title;

      // Tampilkan pieces
      const piecesDiv = document.getElementById("pieces");
      piecesDiv.innerHTML = "";
      let shuffled = [...level.pieces].sort(() => Math.random() - 0.5);
      shuffled.forEach(p => {
        let div = document.createElement("div");
        div.className = "piece";
        div.id = p.id;
        div.innerText = p.text;
        div.draggable = true;
        div.addEventListener('dragstart', dragStart);
        piecesDiv.appendChild(div);
      });

      // Tampilkan slots
      const slotsDiv = document.getElementById("slots");
      slotsDiv.innerHTML = "";
      level.order.forEach(id => {
        let slot = document.createElement("div");
        slot.className = "slot";
        slot.dataset.order = id;
        slot.addEventListener('dragover', dragOver);
        slot.addEventListener('drop', drop);
        slotsDiv.appendChild(slot);
      });

      document.getElementById("feedback").innerHTML = "";
    }

    // Drag & Drop
    function dragStart(e) { e.dataTransfer.setData('text/plain', e.target.id); }
    function dragOver(e) { e.preventDefault(); }
    function drop(e) {
      e.preventDefault();
      const id = e.dataTransfer.getData('text/plain');
      const piece = document.getElementById(id);
      if (e.target.classList.contains('slot') && e.target.children.length === 0) {
        e.target.appendChild(piece);
      }
    }

    // Cek Jawaban
    function checkAnswer() {
      const slots = document.querySelectorAll('#slots .slot');
      let correct = true;
      slots.forEach(slot => {
        if (slot.children.length === 0 || slot.children[0].id !== slot.dataset.order) {
          correct = false;
        }
      });

      if (correct) {
        starCount++;
        document.getElementById("feedback").innerHTML = "✅ Benar! Lanjut ke level berikutnya.";
        document.getElementById("starCount").innerText = starCount;

        setTimeout(() => {
          currentLevel++;
          if (currentLevel < levels.length) {
            loadLevel();
          } else {
            document.getElementById("feedback").innerHTML = "🏆 Selamat! Semua level selesai.";
          }
        }, 1500);

      } else {
        document.getElementById("feedback").innerHTML = "❌ Salah! Game Over. Coba lagi!";
        starCount = 0;
        document.getElementById("starCount").innerText = starCount;
        setTimeout(loadLevel, 1500);
      }
    }

    // Bantuan
    function giveHint() {
      const level = levels[currentLevel];
      document.getElementById("feedback").innerHTML = "💡 Hint: " + level.hint;
    }
  </script>
</body>
</html>
