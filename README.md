import socket
import threading

# Server configuration
HOST = '127.0.0.1'
PORT = 12345

# Create a socket
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind((HOST, PORT))
server_socket.listen()

# List to store connected clients
clients = []

# Function to broadcast messages to all connected clients
def broadcast(message, client_socket):
    for client in clients:
        if client != client_socket:
            try:
                client.send(message)
            except:
                client.close()
                remove(client)

# Function to remove a disconnected client
def remove(client_socket):
    if client_socket in clients:
        clients.remove(client_socket)

# Function to handle client connections
def handle_client(client_socket):
    while True:
        try:
            message = client_socket.recv(1024)
            if not message:
                remove(client_socket)
                break
            broadcast(message, client_socket)
        except:
            continue

# Main server loop
while True:
    client_socket, client_address = server_socket.accept()
    clients.append(client_socket)
    print(f"{client_address[0]}:{client_address[1]} connected.")
    client_thread = threading.Thread(target=handle_client, args=(client_socket,))
    client_thread.start()
    
