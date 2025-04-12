from flask import Flask, request, jsonify
from datetime import datetime
import smtplib
import socket

app = Flask(__name__)

# Sample user data (Demo)
USER_DATA = {
    "username": "instauser",
    "last_ip": "85.100.45.22",
    "last_login": "2025-04-10 15:32:00"
}

def send_alert(email, ip, location):
    # Done with SMTP or SMS API in real application
    print(f"⚠️ Uyarı! Şüpheli giriş tespit edildi.\nIP: {ip}\nKonum: {location}")

def get_location(ip):
    # Dummy IP to location
    return "İstanbul, Türkiye" if ip.startswith("85.") else "Unknown Location"

@app.route('/login', methods=['POST'])
def login():
    username = request.form.get("username")
    ip_address = request.remote_addr

    if username != USER_DATA["username"]:
        return jsonify({"status": "error", "message": "Username invalid."})

    if ip_address != USER_DATA["last_ip"]:
        location = get_location(ip_address)
        send_alert("user@mail.com", ip_address, location)
        return jsonify({
            "status": "alert",
            "message": "Suspicious session detected. Login blocked.",
            "ip": ip_address,
            "location": location
        })
    
    USER_DATA["last_login"] = str(datetime.now())
    return jsonify({"status": "success", "message": "Entry successful."})

if __name__ == '__main__':
    app.run(debug=True)
