<!DOCTYPE html>
<html>
<head>
  <title>Zoya Admin Panel</title>

  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: linear-gradient(135deg, #0f172a, #020617);
      color: white;
      text-align: center;
    }

    h1 {
      margin-top: 20px;
      font-size: 28px;
      color: #38bdf8;
    }

    .container {
      max-width: 600px;
      margin: auto;
      padding: 20px;
    }

    input {
      padding: 12px;
      width: 80%;
      border-radius: 10px;
      border: none;
      outline: none;
      background: #1e293b;
      color: white;
      margin-bottom: 10px;
    }

    button {
      padding: 12px 20px;
      border: none;
      border-radius: 12px;
      background: linear-gradient(45deg, #06b6d4, #3b82f6);
      color: white;
      font-weight: bold;
      cursor: pointer;
      margin: 5px;
      transition: 0.3s;
    }

    button:hover {
      transform: scale(1.05);
      box-shadow: 0 0 15px #3b82f6;
    }

    #keyDisplay {
      margin: 15px;
      font-size: 20px;
      color: #22c55e;
    }

    .card {
      background: rgba(255,255,255,0.05);
      backdrop-filter: blur(10px);
      border-radius: 15px;
      padding: 15px;
      margin-top: 10px;
      text-align: left;
      animation: fadeIn 0.5s ease;
    }

    .row {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 8px;
    }

    .toggle {
      cursor: pointer;
      padding: 5px 10px;
      border-radius: 8px;
      background: #ef4444;
    }

    .enabled {
      background: #22c55e !important;
    }

    @keyframes fadeIn {
      from {opacity:0; transform: translateY(10px);}
      to {opacity:1; transform: translateY(0);}
    }

  </style>
</head>

<body>

<h1>Zoya Admin Panel</h1>

<div class="container">

  <input id="username" placeholder="Enter User Name"/>
  <br>

  <button onclick="generateKey()">Generate Key</button>

  <h2 id="keyDisplay"></h2>

  <button onclick="saveKey()">Save Key</button>

  <p id="status"></p>

  <hr>

  <h2>All Keys</h2>

  <div id="keysList"></div>

</div>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
import { getFirestore, doc, setDoc, getDoc, collection, getDocs, updateDoc } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

const firebaseConfig = {
  apiKey: "AIzaSyDdXDEqjuYNNFyapUE6cvru-9KsFkj9bh0",
  authDomain: "max-6f064.firebaseapp.com",
  projectId: "max-6f064"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

let currentKey = "";

// 🔑 Generate Key
window.generateKey = function () {
  const part1 = Math.floor(100 + Math.random() * 900);

  let part2 = "";
  for (let i = 0; i < 10; i++) {
    part2 += Math.floor(Math.random() * 10);
  }

  currentKey = `MAX-${part1}-${part2}`;
  document.getElementById("keyDisplay").innerText = currentKey;
};

// 💾 Save Key
window.saveKey = async function () {

  const name = document.getElementById("username").value;

  if (!name || !currentKey) {
    alert("Fill name & generate key");
    return;
  }

  const ref = doc(db, "accessKeys", currentKey);
  const snap = await getDoc(ref);

  if (snap.exists()) {
    alert("Key exists!");
    return;
  }

  await setDoc(ref, {
    name: name,
    deviceId: "",
    enabled: true
  });

  document.getElementById("status").innerText = "Saved!";
  loadKeys();
};

// 📥 Load All Keys
async function loadKeys() {
  const list = document.getElementById("keysList");
  list.innerHTML = "";

  const querySnapshot = await getDocs(collection(db, "accessKeys"));

  querySnapshot.forEach((docSnap) => {
    const data = docSnap.data();
    const key = docSnap.id;

    const card = document.createElement("div");
    card.className = "card";

    card.innerHTML = `
      <div><b>${key}</b></div>

      <div class="row">
        <input value="${data.name}" onchange="updateName('${key}', this.value)" />
      </div>

      <div class="row">
        <span>Status:</span>
        <div class="toggle ${data.enabled ? 'enabled' : ''}" onclick="toggleKey('${key}', ${data.enabled})">
          ${data.enabled ? 'Enabled' : 'Disabled'}
        </div>
      </div>
    `;

    list.appendChild(card);
  });
}

// 🔄 Toggle Enable
window.toggleKey = async function (key, current) {
  await updateDoc(doc(db, "accessKeys", key), {
    enabled: !current
  });
  loadKeys();
};

// ✏️ Update Name
window.updateName = async function (key, newName) {
  await updateDoc(doc(db, "accessKeys", key), {
    name: newName
  });
};

loadKeys();

</script>

</body>
</html>
