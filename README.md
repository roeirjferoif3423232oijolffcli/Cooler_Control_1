<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>کنترل کولر</title>
    <style>
        body { 
            font-family: Arial; 
            text-align: center; 
            padding: 20px; 
            background: linear-gradient(45deg, #74b9ff, #0984e3);
            min-height: 100vh;
            margin: 0;
        }
        .container {
            background: white;
            max-width: 400px;
            margin: 50px auto;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
        }
        h1 { color: #333; margin-bottom: 30px; }
        .btn {
            width: 100%;
            padding: 15px;
            margin: 10px 0;
            font-size: 18px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
        }
        .btn:hover { transform: scale(1.05); }
        .btn-on { background: #00b894; color: white; }
        .btn-off { background: #e17055; color: white; }
        .speed-section {
            margin: 20px 0;
            padding: 20px;
            background: #f8f9fa;
            border-radius: 10px;
        }
        .slider {
            width: 100%;
            height: 8px;
            border-radius: 5px;
            background: #ddd;
            outline: none;
            margin: 15px 0;
        }
        .status {
            margin-top: 20px;
            padding: 15px;
            border-radius: 8px;
            font-weight: bold;
            display: none;
        }
        .success { background: #d1f2eb; color: #00b894; }
        .error { background: #fadbd8; color: #e74c3c; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🌀 کنترل کولر</h1>
        
        <button class="btn btn-on" onclick="sendCommand('cooler_on')">
            ❄️ روشن کردن کولر
        </button>
        
        <button class="btn btn-off" onclick="sendCommand('cooler_off')">
            🔴 خاموش کردن کولر
        </button>
        
        <div class="speed-section">
            <h3>💨 سرعت فن</h3>
            <input type="range" class="slider" min="1" max="5" value="3" 
                   id="fanSpeed" onchange="setFanSpeed(this.value)">
            <div>سرعت: <span id="speedValue">3</span></div>
        </div>
        
        <button class="btn" style="background: #6c5ce7; color: white;" onclick="getStatus()">
            📊 وضعیت فعلی
        </button>
        
        <div id="status" class="status"></div>
    </div>

    <script>
        // آدرس IP آردوینو شما (بعداً تغییر بدید)
        const ESP_IP = 'http://192.168.1.100'; // این رو بعداً عوض کنید
        
        function sendCommand(cmd) {
            showStatus('در حال ارسال دستور...', 'success');
            
            // ارسال دستور به ESP
            const url = `${ESP_IP}/api/command?cmd=${cmd}`;
            
            fetch(url, { 
                method: 'GET',
                mode: 'no-cors' 
            })
            .then(() => {
                showStatus(`دستور ${cmd} ارسال شد!`, 'success');
                
                // ذخیره دستور در localStorage برای نمایش
                localStorage.setItem('lastCommand', cmd);
                localStorage.setItem('lastCommandTime', new Date().toLocaleString('fa-IR'));
            })
            .catch(error => {
                showStatus('خطا در ارسال دستور!', 'error');
                console.error('Error:', error);
            });
        }
        
        function setFanSpeed(speed) {
            document.getElementById('speedValue').innerText = speed;
            sendCommand(`fan_speed&speed=${speed}`);
        }
        
        function getStatus() {
            const lastCmd = localStorage.getItem('lastCommand') || 'هیچ';
            const lastTime = localStorage.getItem('lastCommandTime') || 'نامشخص';
            
            showStatus(`آخرین دستور: ${lastCmd} در ${lastTime}`, 'success');
        }
        
        function showStatus(message, type) {
            const statusDiv = document.getElementById('status');
            statusDiv.textContent = message;
            statusDiv.className = `status ${type}`;
            statusDiv.style.display = 'block';
            
            setTimeout(() => {
                statusDiv.style.display = 'none';
            }, 4000);
        }
        
        // نمایش آدرس فعلی
        window.onload = function() {
            console.log('سایت آماده است!');
            console.log('آدرس ESP را در متغیر ESP_IP تنظیم کنید');
        }
    </script>
</body>
</html>
