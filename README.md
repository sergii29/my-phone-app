<!DOCTYPE html>
<html>
<head>
    <title>Private Line</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>
    <style>
        body { font-family: sans-serif; text-align: center; padding-top: 50px; background: #f0f0f0; }
        button { padding: 20px; font-size: 1.2rem; border-radius: 10px; border: none; cursor: pointer; }
        .call-btn { background: #28a745; color: white; }
        .hangup-btn { background: #dc3545; color: white; margin-top: 20px; }
    </style>
</head>
<body>

    <h2>Приватная Линия</h2>
    <div id="status">Ожидание подключения...</div>
    <br>
    <button class="call-btn" onclick="makeCall()">ПОЗВОНИТЬ</button>
    <br>
    <audio id="remoteAudio" autoplay></audio>

    <script>
        // Настройка: один телефон будет 'phone1', другой 'phone2'
        const myId = 'phone1'; 
        const targetId = 'phone2'; 

        const peer = new Peer(myId); // Инициализация связи

        peer.on('open', (id) => {
            document.getElementById('status').innerText = "Готов к работе. Мой ID: " + id;
        });

        // Слушаем входящие звонки
        peer.on('call', (call) => {
            navigator.mediaDevices.getUserMedia({video: false, audio: true}).then((stream) => {
                call.answer(stream); // Отвечаем своим звуком
                call.on('stream', (remoteStream) => {
                    document.getElementById('remoteAudio').srcObject = remoteStream;
                    document.getElementById('status').innerText = "В разговоре...";
                });
            });
        });

        // Функция исходящего звонка
        function makeCall() {
            navigator.mediaDevices.getUserMedia({video: false, audio: true}).then((stream) => {
                const call = peer.call(targetId, stream);
                call.on('stream', (remoteStream) => {
                    document.getElementById('remoteAudio').srcObject = remoteStream;
                    document.getElementById('status').innerText = "Вызываю...";
                });
            });
        }
    </script>
</body>
</html>
