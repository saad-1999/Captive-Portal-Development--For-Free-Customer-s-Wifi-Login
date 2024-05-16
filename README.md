# Captive-Portal-Development--For-Free-Customer-s-Wifi-Login

<!-- templates/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WiFi Login Portal</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='styles.css') }}">
</head>
<body>
    <h1>Free WiFi Login Portal</h1>
    <form action="/generate_qr" method="post">
        <label for="contact">Contact Number:</label>
        <input type="text" id="contact" name="contact" required><br><br>
        <label for="age">Age:</label>
        <input type="number" id="age" name="age" required><br><br>
        <label for="address">Address:</label>
        <input type="text" id="address" name="address" required><br><br>
        <button type="submit">Get Free WiFi</button>
    </form>
</body>
</html>
/* static/styles.css */
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    text-align: center;
}

h1 {
    margin-top: 50px;
}

form {
    margin-top: 30px;
}

label {
    font-weight: bold;
}

input[type="text"],
input[type="number"] {
    padding: 8px;
    margin: 5px;
}

button {
    padding: 10px 20px;
    background-color: #007bff;
    color: #fff;
    border: none;
    cursor: pointer;
}

img {
    margin-top: 30px;
    max-width: 300px;
    height: auto;
}
# app.py
from flask import Flask, render_template, request
import qrcode

app = Flask(__name__)

# Create a dictionary to store customer details and QR code filenames
customer_data = {}

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/generate_qr', methods=['POST'])
def generate_qr():
    # Get customer information from the form
    contact = request.form['contact']
    age = request.form['age']
    address = request.form['address']

    # Generate WiFi network name and password
    wifi_name = "MyWiFiNetwork"
    wifi_password = "MySecretPassword123"

    # Create the content for the QR code
    qr_content = f"WiFi Name: {wifi_name}\nPassword: {wifi_password}\nContact: {contact}\nAge: {age}\nAddress: {address}"

    # Generate the QR code
    qr = qrcode.make(qr_content)

    # Save the QR code to a file
    qr_file = f"static/wifi_qr_{contact}.png"
    qr.save(qr_file)

    # Store customer details and QR code filename in local memory
    customer_data[contact] = {
        'age': age,
        'address': address,
        'qr_file': qr_file
    }

    # Render the QR code page with the generated QR code image
    return render_template('qr.html', qr_file=qr_file)

@app.route('/customer_details/<contact>')
def get_customer_details(contact):
    return customer_data.get(contact, {})

if __name__ == '__main__':
    app.run(debug=True)
