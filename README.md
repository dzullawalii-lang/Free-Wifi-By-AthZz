# Free-Wifi-By-AthZz
Free wifi
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>DIZX · Deteksi Jaringan Real (Bukan Simulasi)</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            background: radial-gradient(circle at 20% 30%, #0a0f1a, #020107);
            font-family: 'Segoe UI', system-ui, 'Inter', sans-serif;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        .glass-card {
            background: rgba(12, 12, 28, 0.85);
            backdrop-filter: blur(12px);
            border-radius: 2rem;
            border: 1px solid rgba(255, 45, 117, 0.5);
            max-width: 550px;
            width: 100%;
            padding: 1.8rem;
            box-shadow: 0 25px 40px -12px black;
        }
        h1 {
            font-size: 1.7rem;
            font-weight: 800;
            background: linear-gradient(135deg, #ff2d75, #ff98bb);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            text-align: center;
            margin-bottom: 0.25rem;
        }
        .sub {
            text-align: center;
            color: #aaa;
            font-size: 0.7rem;
            margin-bottom: 1.5rem;
            border-bottom: 1px dashed #ff2d7544;
            display: inline-block;
            width: 100%;
            padding-bottom: 0.5rem;
        }
        .info-box {
            background: #01010e;
            border-radius: 1.2rem;
            padding: 1rem;
            margin: 1rem 0;
            border-left: 4px solid #ff2d75;
        }
        .info-label {
            font-size: 0.7rem;
            text-transform: uppercase;
            color: #ff98bb;
            letter-spacing: 1px;
        }
        .info-value {
            font-size: 1rem;
            font-weight: 600;
            word-break: break-all;
            margin-top: 4px;
            color: #f0f0f0;
            font-family: monospace;
        }
        .badge {
            display: inline-block;
            background: #ff2d7522;
            border-radius: 30px;
            padding: 4px 12px;
            font-size: 0.7rem;
            margin-top: 6px;
            color: #ffaa99;
        }
        button {
            background: linear-gradient(95deg, #ff2d75, #ff5e9e);
            border: none;
            width: 100%;
            padding: 12px;
            border-radius: 2rem;
            font-weight: bold;
            color: white;
            font-size: 1rem;
            cursor: pointer;
            margin-top: 0.5rem;
        }
        button:active {
            transform: scale(0.97);
        }
        .status {
            font-size: 0.75rem;
            text-align: center;
            margin-top: 1rem;
            color: #ffccaa;
        }
        footer {
            font-size: 0.6rem;
            text-align: center;
            color: #6a6a8a;
            margin-top: 1.2rem;
        }
        .online {
            color: #6effb0;
        }
        .offline {
            color: #ff8888;
        }
    </style>
</head>
<body>
<div class="glass-card">
    <h1>🌐 DETEKSI JARINGAN REAL</h1>
    <div class="sub">Bukan simulasi — data diambil langsung dari perangkat & server</div>

    <div class="info-box">
        <div class="info-label">📶 STATUS KONEKSI INTERNET</div>
        <div class="info-value" id="onlineStatus">Memeriksa...</div>
        <div class="badge" id="pingInfo">-</div>
    </div>

    <div class="info-box">
        <div class="info-label">🌍 IP PUBLIK & LOKASI PERKIRAAN</div>
        <div class="info-value" id="ipDisplay">Mengambil data...</div>
        <div class="badge" id="locationHint">Lokasi berdasarkan IP (perkiraan)</div>
    </div>

    <div class="info-box">
        <div class="info-label">📡 JENIS KONEKSI (Browser API)</div>
        <div class="info-value" id="connectionType">-</div>
    </div>

    <div class="info-box">
        <div class="info-label">🔌 INTERFACE JARINGAN (Lokal)</div>
        <div class="info-value" id="localIP">-</div>
    </div>

    <button id="refreshBtn">🔄 CEK ULANG (REAL TIME)</button>
    <div class="status" id="statusMsg">Data diambil secara real — bukan simulasi</div>
    <footer>✅ Informasi jaringan yang ditampilkan BENAR-BENAR nyata dari perangkat & server eksternal. Tidak ada efek palsu.</footer>
</div>

<script>
    (function() {
        const onlineSpan = document.getElementById('onlineStatus');
        const ipSpan = document.getElementById('ipDisplay');
        const connectionSpan = document.getElementById('connectionType');
        const localIpSpan = document.getElementById('localIP');
        const pingInfoSpan = document.getElementById('pingInfo');
        const refreshBtn = document.getElementById('refreshBtn');
        const statusMsg = document.getElementById('statusMsg');

        // 1. Status online/offline (real)
        function updateOnlineStatus() {
            const isOnline = navigator.onLine;
            if (isOnline) {
                onlineSpan.innerHTML = '<span class="online">✅ ONLINE (Terhubung ke internet)</span>';
                onlineSpan.style.color = '#6effb0';
            } else {
                onlineSpan.innerHTML = '<span class="offline">❌ OFFLINE (Tidak ada koneksi internet)</span>';
                onlineSpan.style.color = '#ff8888';
            }
        }
        window.addEventListener('online', updateOnlineStatus);
        window.addEventListener('offline', updateOnlineStatus);
        updateOnlineStatus();

        // 2. Jenis koneksi (navigator.connection) real browser API
        function getConnectionType() {
            const conn = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
            if (conn) {
                const type = conn.effectiveType || conn.type || 'unknown';
                const downlink = conn.downlink ? ` (${conn.downlink} Mbps)` : '';
                return `${type.toUpperCase()}${downlink}`;
            }
            return 'Tidak terdeteksi (browser tidak support)';
        }
        function updateConnection() {
            connectionSpan.innerText = getConnectionType();
        }

        // 3. Mendapatkan IP publik dan lokasi perkiraan (real API)
        async function fetchPublicIP() {
            try {
                const res = await fetch('https://api.ipify.org?format=json');
                const data = await res.json();
                const ip = data.ip;
                // Ambil lokasi perkiraan berdasarkan ip (ip-api.com, gratis, tanpa kunci)
                const geoRes = await fetch(`https://ip-api.com/json/${ip}?fields=status,country,city,region,isp`);
                let locationText = '';
                if (geoRes.ok) {
                    const geo = await geoRes.json();
                    if (geo.status === 'success') {
                        locationText = `${geo.city ? geo.city + ', ' : ''}${geo.regionName ? geo.regionName + ', ' : ''}${geo.country}`;
                        if (geo.isp) locationText += ` (ISP: ${geo.isp})`;
                    } else {
                        locationText = 'Lokasi tidak tersedia';
                    }
                } else {
                    locationText = 'Gagal ambil lokasi';
                }
                ipSpan.innerHTML = `<strong>${ip}</strong><br><span style="font-size:0.75rem; color:#aaa;">📍 Perkiraan lokasi: ${locationText}</span>`;
                return ip;
            } catch (err) {
                ipSpan.innerHTML = 'Gagal mengambil IP publik (koneksi bermasalah atau offline)';
                return null;
            }
        }

        // 4. Mendapatkan IP lokal (melalui WebRTC, real)
        async function getLocalIP() {
            return new Promise((resolve) => {
                const pc = new RTCPeerConnection({ iceServers: [] });
                pc.createDataChannel('');
                pc.createOffer().then(offer => pc.setLocalDescription(offer));
                pc.onicecandidate = (ice) => {
                    if (!ice || !ice.candidate || !ice.candidate.candidate) return;
                    const candidate = ice.candidate.candidate;
                    const ipRegex = /([0-9]{1,3}\.){3}[0-9]{1,3}/;
                    const match = ipRegex.exec(candidate);
                    if (match) {
                        resolve(match[0]);
                        pc.close();
                    }
                };
                setTimeout(() => resolve('Tidak terdeteksi (WebRTC block)'), 2000);
            });
        }

        // 5. Ping sederhana (periksa latency ke server)
        async function checkPing() {
            const start = Date.now();
            try {
                await fetch('https://api.ipify.org?format=json', { cache: 'no-store', mode: 'no-cors' });
                const end = Date.now();
                pingInfoSpan.innerText = `🏓 Latency ~ ${end - start} ms (ke server ipify)`;
            } catch (e) {
                pingInfoSpan.innerText = '🏓 Ping tidak tersedia (offline / blokir)';
            }
        }

        // Fungsi refresh semua data
        async function refreshAll() {
            statusMsg.innerHTML = "🔄 Mengambil data jaringan real...";
            updateOnlineStatus();
            updateConnection();
            await fetchPublicIP();
            const localIp = await getLocalIP();
            localIpSpan.innerText = localIp;
            await checkPing();
            statusMsg.innerHTML = "✅ Data jaringan REAL terakhir diperbarui (bukan simulasi)";
        }

        refreshBtn.addEventListener('click', () => {
            refreshAll();
        });

        // auto refresh awal
        refreshAll();

        // deteksi perubahan koneksi (jika ada)
        if (navigator.connection) {
            navigator.connection.addEventListener('change', () => {
                updateConnection();
                statusMsg.innerHTML = "⚠️ Jenis koneksi berubah (real)";
            });
        }
    })();
</script>
</body>
</html>
