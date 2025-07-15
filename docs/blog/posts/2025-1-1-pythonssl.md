---
slug: pythonssl
date: 2025-01-01
title: Secure Python Server-Client Communication with OpenSSL 
tags: [python, openssl]
---

## Setting Up a Secure Python Server and Client Communication Using OpenSSL-Generated Certificates

To create a Certificate Authority (CA) and generate server using OpenSSL, followed by implementing a Python server and client that use these certificates for secure communication, you can follow these steps:

<!-- more -->

### Step 1: Install OpenSSL 

If OpenSSL is not installed, you can install it using:
 
- **Ubuntu:**  `sudo apt-get install openssl`
 
- **MacOS:**  `brew install openssl`


### Step 2: Regenerate the Certificate Authority (CA) 
 
1. **Generate the private key for the CA:** 

```bash
openssl genpkey -algorithm RSA -out ca-key.pem -aes256
```

You will be prompted to set a passphrase for the private key.
 
2. **Generate the CA certificate:** 

```bash
openssl req -x509 -new -nodes -key ca-key.pem -sha256 -days 365 -out ca-cert.pem
```

You will be prompted to enter details for the certificate. These details can be anything since this is your root CA.
Step 2: Regenerate the Server Certificate with `localhost` as CN 
1. **Generate the private key for the server:** 

```bash
openssl genpkey -algorithm RSA -out server-key.pem
```
 
2. **Generate a Certificate Signing Request (CSR) for the server:** 

```bash
openssl req -new -key server-key.pem -out server.csr
```
When prompted for the `Common Name (CN)`, enter `localhost`.
 
3. **Sign the server certificate with the CA:** 

```bash
openssl x509 -req -in server.csr -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -out server-cert.pem -days 365 -sha256
```

### Step 3: Create the Python Server 
Create a Python script for the server (`server.py`):

```python
import ssl
import socket

def create_server():
    # Server setup
    context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
    context.load_cert_chain(certfile="server-cert.pem", keyfile="server-key.pem")

    bindsocket = socket.socket()
    bindsocket.bind(('localhost', 8443))
    bindsocket.listen(5)

    while True:
        print("Server is waiting for a connection...")
        newsocket, fromaddr = bindsocket.accept()
        conn = context.wrap_socket(newsocket, server_side=True)
        try:
            print("Connection established:", fromaddr)
            data = conn.recv(1024)
            print("Received data:", data.decode())
            conn.sendall(b"Hello, client!")
        finally:
            conn.shutdown(socket.SHUT_RDWR)
            conn.close()

if __name__ == "__main__":
    create_server()
```

### Step 4: Create the Python Client 
Create a Python script for the client (`client.py`). This version disables hostname verification for testing purposes:

```python
import ssl
import socket

def create_client():
    # Client setup
    context = ssl.SSLContext(ssl.PROTOCOL_TLS_CLIENT)
    context.load_verify_locations(cafile="ca-cert.pem")

    # Disable hostname verification (for testing purposes)
    context.check_hostname = False

    # Connect to the server
    conn = context.wrap_socket(socket.socket(socket.AF_INET), server_hostname='localhost')
    conn.connect(('localhost', 8443))

    # Send a message to the server
    conn.sendall(b"Hello, server!")
    data = conn.recv(1024)
    print("Received data:", data.decode())

    conn.close()

if __name__ == "__main__":
    create_client()
```

### Step 5: Run the Server and Client 
 
1. **Start the server:** 

```bash
python3 server.py
```
 
2. **Run the client in another terminal:** 

```bash
python3 client.py
```

### Explanation 
 
- **CA Generation:**  We created a CA that can sign server and client certificates.
 
- **Server Certificate:**  The server's certificate is signed by the CA and has `localhost` as the Common Name (CN).
 
- **Server and Client Code:**  The server and client code uses the certificates generated, with the client disabling hostname verification for ease of testing.

This setup should now work without any certificate verification errors, allowing the client to connect securely to the server.

