<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Private Chat Room</title>
  <style>
    body {
      font-family: Arial, sans-serif;
    }
    #chat {
      display: none;
    }
    #messages {
      border: 1px solid #ccc;
      padding: 10px;
      height: 300px;
      overflow-y: scroll;
    }
    #message-input {
      width: 100%;
      box-sizing: border-box;
    }
  </style>
</head>
<body>
  <div id="welcome">
    <button id="create-room">Create Room</button>
    <input type="text" id="room-id" placeholder="Enter Room ID">
    <button id="join-room">Join Room</button>
  </div>
  <div id="chat">
    <div id="messages"></div>
    <input type="text" id="message-input" placeholder="Type your message here">
  </div>

  <script src="https://cdn.jsdelivr.net/npm/simple-peer/simplepeer.min.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.6.8/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.6.8/firebase-firestore.js"></script>
  <script>
    // Your Firebase configuration
    const firebaseConfig = {
      apiKey: "AIzaSyBGNgPyfzcbYTpoBq8VDCkDfEpPk4QzF0Q",
      authDomain: "just-the-two-of-us-f48a1.firebaseapp.com",
      projectId: "just-the-two-of-us-f48a1",
      storageBucket: "just-the-two-of-us-f48a1.appspot.com",
      messagingSenderId: "443705002983",
      appId:"1:443705002983:web:2cf8340533b909fba74b42",
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.firestore();

    const createRoomButton = document.getElementById('create-room');
    const joinRoomButton = document.getElementById('join-room');
    const roomIdInput = document.getElementById('room-id');
    const welcomeDiv = document.getElementById('welcome');
    const chatDiv = document.getElementById('chat');
    const messagesDiv = document.getElementById('messages');
    const messageInput = document.getElementById('message-input');

    let roomId;
    let peer;

    createRoomButton.addEventListener('click', async () => {
      const docRef = await db.collection('rooms').add({});
      roomId = docRef.id;
      alert(`Room created. ID: ${roomId}`);
      initializePeer(true);
    });

    joinRoomButton.addEventListener('click', () => {
      roomId = roomIdInput.value;
      if (roomId) {
        initializePeer(false);
      } else {
        alert('Please enter a room ID');
      }
    });

    function initializePeer(isInitiator) {
      peer = new SimplePeer({ initiator: isInitiator, trickle: false });

      peer.on('signal', async (data) => {
        await db.collection('rooms').doc(roomId).set({ signal: JSON.stringify(data) });
      });

      peer.on('connect', () => {
        welcomeDiv.style.display = 'none';
        chatDiv.style.display = 'block';
      });

      peer.on('data', (data) => {
        const message = document.createElement('div');
        message.textContent = `Her: ${data}`;
        messagesDiv.appendChild(message);
      });

      db.collection('rooms').doc(roomId).onSnapshot((doc) => {
        if (doc.exists) {
          const data = doc.data();
          if (data.signal && data.signal !== peer.localDescription) {
            peer.signal(JSON.parse(data.signal));
          }
        }
      });

      messageInput.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') {
          const message = messageInput.value;
          peer.send(message);
          const myMessage = document.createElement('div');
          myMessage.textContent = `You: ${message}`;
          messagesDiv.appendChild(myMessage);
          messageInput.value = '';
        }
      });
    }
  </script>
</body>
</html>
