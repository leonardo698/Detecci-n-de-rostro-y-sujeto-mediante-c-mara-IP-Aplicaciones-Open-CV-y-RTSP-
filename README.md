from flask import Flask
from flask import render_template
from flask import Response
import cv2

app = Flask(__name__)
rostros_cascada = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')
humano_cascada = cv2.CascadeClassifier('haarcascade_fullbody.xml')
captura = cv2.VideoCapture(0) #IP Camera

def generate():
    while True:
        _, img = captura.read()

    gris = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    rostros = rostros_cascada.detectMultiScale(gris, 1.3, 5)
    humano = humano_cascada.detectMultiScale(gris, 1.2, 5)
 
    for (x, y, w, h) in rostros:
        cv2.rectangle(img, (x, y), (x + w, y + h), (0, 0, 255), 2) 
    for (x, y, w, h) in humano:
        cv2.rectangle(img, (x, y), (x + w, y + h), (0, 0, 255), 2)
        (flag, encodedImage)=cv2.inmencode(".jpg", frame)
        if not flag:
            continue
            yield(b'--frame\r\n' b'Content-Type: image/jpeg\r\n\r\n' +
                    bytearray(encodedImage) + b'\r\n')

@app.route('/')
def index():
    return render_template('index.html')
    
@app.route("/video_feed")
def video_feed():
     return Response(generate(),
          mimetype = "multipart/x-mixed-replace; boundary=frame")



# Configuración básica
if __name__ == '__main__':
    # Activamos debug y configuramos para que sea accesible desde cualquier dispositivo
    app.run(debug=True, host='0.0.0.0')

cap.release()
