from flask import Flask, render_template_string, request
from flask_socketio import SocketIO, send

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your_secret_key'
socketio = SocketIO(app)

HTML_TEMPLATE = """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Real-Time Chat App</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        #messages { border: 1px solid #ccc; height: 300px; overflow-y: scroll; padding: 10px; }
        input { width: 80%; padding: 10px; }
        button { padding: 10px; }
    </style>
</head>
<body>
    <h1>Real-Time Chat</h1>
    <div id="messages"></div>
    <br>
    <input id="messageInput" type="text" placeholder="Type a message...">
    <button onclick="sendMessage()">Send</button>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/4.0.1/socket.io.min.js"></script>
    <script>
        var socket = io();
        var messages = document.getElementById('messages');
        socket.on('message', function(msg){
            var p = document.createElement('p');
            p.textContent = msg;
            messages.appendChild(p);
            messages.scrollTop = messages.scrollHeight;
        });
        function sendMessage(){
            var input = document.getElementById('messageInput');
            if(input.value.trim() !== ""){
                socket.send(input.value);
                input.value = '';
            }
        }
    </script>
</body>
</html>
"""

@app.route("/")
def home():
    return render_template_string(HTML_TEMPLATE)

@socketio.on('message')
def handle_message(msg):
    send(msg, broadcast=True)

if __name__ == "__main__":
    socketio.run(app, debug=True)

