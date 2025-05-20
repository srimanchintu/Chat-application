import socket
import threading

# Server configuration
HOST = '127.0.0.1'
PORT = 55555

# Create socket
server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind((HOST, PORT))
server.listen()

clients = []
usernames = []

def broadcast(message, _client):
    for client in clients:
        if client != _client:
            client.send(message)

def handle_client(client):
    while True:
        try:
            message = client.recv(1024)
            broadcast(message, client)
        except:
            index = clients.index(client)
            clients.remove(client)
            client.close()
            username = usernames[index]
            broadcast(f"{username} left the chat.".encode('utf-8'), client)
            usernames.remove(username)
            break

def receive_connections():
    print(f"Server is running on {HOST}:{PORT}")
    while True:
        client, address = server.accept()
        print(f"Connected with {str(address)}")

        client.send("USERNAME".encode('utf-8'))
        username = client.recv(1024).decode('utf-8')
        usernames.append(username)
        clients.append(client)

        print(f"Username is {username}")
        broadcast(f"{username} joined the chat!".encode('utf-8'), client)
        client.send("Connected to the server.".encode('utf-8'))

        thread = threading.Thread(target=handle_client, args=(client,))
        thread.start()

receive_connections()

## i have done this task 1 chat application using with python 
