<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MAX AI - Firestore Admin</title>

    <!-- Firebase SDK (Error Fix: Inhe sabse upar rakhna zaroori hai) -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>

    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #0a0a0a; color: #fff; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; }
        .card { background: #151515; padding: 40px; border-radius: 20px; border: 1px solid #333; width: 420px; text-align: center; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        h2 { color: #00ff88; letter-spacing: 2px; margin-bottom: 5px; }
        .sub { font-size: 11px; color: #777; margin-bottom: 25px; }
        .main-btn { width: 100%; padding: 15px; background: #00ff88; color: #000; border: none; border-radius: 10px; font-weight: bold; cursor: pointer; font-size: 16px; }
        .result-box { margin-top: 25px; padding: 20px; background: #000; border-radius: 12px; display: none; border: 1px solid #00ff88; }
        #keyOutput { font-family: monospace; color: #00ff88; font-size: 13px; word-break: break-all; margin-bottom: 15px; }
        .btn-group { display: flex; gap: 10px; }
        .btn-group button { flex: 1; padding: 10px; border-radius: 6px; cursor: pointer; font-weight: bold; border: none; }
        #status { margin-top: 20px; font-size: 13px; }
    </style>
</head>
<body>

<div class="card">
    <h2>MAX AI CONTROL</h2>
    <div class="sub">PROJECT: nexus-916d7 | FIRESTORE</div>
    
    <button class="main-btn" onclick="generateKey()">GENERATE NEW KEY</button>

    <div id="resultArea" class="result-box">
        <div id="keyOutput"></div>
        <div class="btn-group">
            <button onclick="copyKey()" style="background: #333; color: #fff;">📋 COPY</button>
            <button onclick="saveToFirestore()" style="background: #fff; color: #000;">💾 SAVE TO CLOUD</button>
        </div>
    </div>

    <div id="status"></div>
</div>

<script>
    // --- Global Variables ---
    var currentKey = "";
    var db;

    // Firebase Config
    var firebaseConfig = {
      apiKey: "AIzaSyCiTyweZT60vWzmt2V1xEmU0zxzvIe23MQ",
      authDomain: "://firebaseapp.com",
      projectId: "nexus-916d7",
      storageBucket: "nexus-916d7.firebasestorage.app",
      messagingSenderId: "800875411415",
      appId: "1:800875411415:web:e0d4ec20da1ca5063abd92"
    };

    // Initialize Firebase with Error Handling
    function initFirebase() {
        try {
            if (typeof firebase !== 'undefined') {
                if (!firebase.apps.length) {
                    firebase.initializeApp(firebaseConfig);
                }
                db = firebase.firestore();
                console.log("Firebase initialized successfully.");
            } else {
                document.getElementById('status').innerText = "❌ Error: Firebase library not loaded. Check Internet!";
            }
        } catch (e) {
            console.error("Init Error: ", e);
        }
    }

    // Call init on load
    window.onload = initFirebase;

    function generateKey() {
        var chars = "ABCDEFGHJKLMNPQRSTUVWXYZ23456789";
        var randomNum = Math.floor(100 + Math.random() * 900);
        var randomStr = "";
        for (var i = 0; i < 20; i++) {
            randomStr += chars.charAt(Math.floor(Math.random() * chars.length));
        }
        currentKey = "MAX-" + randomNum + "-" + randomStr;
        
        document.getElementById('keyOutput').innerText = currentKey;
        document.getElementById('resultArea').style.display = "block";
        document.getElementById('status').innerText = "";
    }

    function copyKey() {
        if (!currentKey) return;
        navigator.clipboard.writeText(currentKey);
        alert("Key Copied!");
    }

    async function saveToFirestore() {
        var status = document.getElementById('status');
        if (!db) {
            status.innerText = "❌ Error: Database not connected!";
            return;
        }

        status.style.color = "#888";
        status.innerText = "⏳ Saving...";

        try {
            // Path: access_keys/{MAX-ID}
            await db.collection("access_keys").doc(currentKey).set({
                enabled: true,
                deviceId: "",
                lastCheck: firebase.firestore.FieldValue.serverTimestamp()
            });
            
            status.style.color = "#00ff88";
            status.innerText = "✅ Successfully saved to nexus-916d7!";
        } catch (e) {
            status.style.color = "#ff4444";
            status.innerText = "❌ Error: " + e.message;
        }
    }
</script>

</body>
</html>
