<!DOCTYPE html>
<html>
<head>
    <title>Наш Звонилка</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>
    <style>
        body { font-family: sans-serif; text-align: center; padding: 20px; background: #121212; color: white; }
        .contact-card { background: #1e1e1e; border: 1px solid #333; padding: 20px; border-radius: 15px; margin-bottom: 20px; }
        button { width: 100%; padding: 15px; border-radius: 10px; border: none; font-size: 1.2rem; cursor: pointer; margin-top: 10px; }
        .call-btn { background: #28a745; color: white; }
        .status { font-size: 0.8rem; color: #888; margin-bottom: 10px; }
    </style>
</head>
<body>

    <h2>Контакты</h2>
    
    <div class="contact-card">
        <div class="status" id="status-pasha">Загрузка...</div>
        <strong>Паша</strong>
        <button class="call-btn" onclick="callUser('pasha')">ПОЗВОНИТЬ ПАШЕ</button>
    </div>

    <div class="contact-card">
        <div class="status" id="status-drug">Загрузка...</div>
        <strong>Друг</strong>
        <button class="call-btn" onclick="callUser('drug')">ПОЗВОНИТЬ ДРУГУ</button>
    </div>

    <audio id="remoteAudio" autoplay></audio>

    <script>
        // Спрашиваем при входе: "Кто ты?"
        const myName = prompt("Введите ваше имя (pasha или drug):").toLowerCase();
        const peer = new Peer(myName);

        peer.on('open', (id) => {
            document.getElementById('status-' + id).innerText = "● В СЕТИ (ЭТО ВЫ)";
        });

        // Слушаем входящие
        peer.on('call', (call) => {
            if (confirm("Вам звонят! Ответить?")) {
                navigator.mediaDevices.getUserMedia({video: false, audio: true}).then((stream) => {
                    call.answer(stream);
                    call.on('stream', (remoteStream) => {
                        document.getElementById('remoteAudio').srcObject = remoteStream;
                    });
                });
            }
        });

        // Функция звонка
        function callUser(targetId) {
            if (targetId === myName) {
                alert("Вы не можете позвонить самому себе!");
                return;
            }
            navigator.mediaDevices.getUserMedia({video: false, audio: true}).then((stream) => {
                const call = peer.call(targetId, stream);
                call.on('stream', (remoteStream) => {
                    document.getElementById('remoteAudio').srcObject = remoteStream;
                });
            });
        }
    </script>
</body>
</html>
