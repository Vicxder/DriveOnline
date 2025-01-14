from flask import Flask, request, jsonify, Response
from flask_cors import CORS
import cv2

app = Flask(__name__)
CORS(app)  # Habilitar CORS para permitir solicitudes de la página web

# Ruta principal
@app.route('/')
def home():
    return "Servidor Flask en ejecución"

# Ruta para manejar comandos
@app.route('/control', methods=['POST'])
def control():
    try:
        command = request.json.get('command')
        if not command:
            return jsonify({"status": "error", "message": "No se recibió ningún comando"}), 400
        
        print(f"Comando recibido: {command}")
        return jsonify({"status": "success", "command": command})
    except Exception as e:
        print(f"Error procesando el comando: {e}")
        return jsonify({"status": "error", "message": str(e)}), 500

# Ruta para transmitir video
def generate_video():
    camera = cv2.VideoCapture(0)  # Usa la cámara por defecto (cambiar índice si hay varias cámaras)
    while True:
        success, frame = camera.read()
        if not success:
            break
        _, buffer = cv2.imencode('.jpg', frame)
        frame = buffer.tobytes()
        yield (b'--frame\r\n'
               b'Content-Type: image/jpeg\r\n\r\n' + frame + b'\r\n')

@app.route('/video_feed')
def video_feed():
    return Response(generate_video(), mimetype='multipart/x-mixed-replace; boundary=frame')

if __name__ == '__main__':
    import os
    port = int(os.environ.get('PORT', 5000))  # Puerto asignado por Render
    app.run(host='0.0.0.0', port=port)

