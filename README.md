Network programming engineering.
# Java-socket-programming (Multi-client chat server)

 Description of what i am creating as a question.
 
 Design and implement a multi-client chat server using socket programming in JAVA or any other language of your choice. The server should be capable of accepting connections from multiple clients simultaneously. Each client should be able to send messages to the server, which will then broadcast the messages to all other connected clients. Implement the server using threads to handle communication with each client independently. 

 This mini-project is created using java hard-coding where the code is runned by CMD (Command Prompt).
 1. Create a folder where we will add the server, cient and clientHandler files. Name: JavaSocketProg
 2. Open a text editor, and create the server file. Name: Server.java 
     Make sure the name of the file is similar to the class name.
     The code:

    import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;

public class Server{
	private ServerSocket serverSocket;

	public Server (ServerSocket serverSocket){
		this.serverSocket = serverSocket;
	}
	public void startServer()
	{
		try{
			while (!serverSocket.isClosed()){
				Socket socket = serverSocket.accept();
				System.out.println("A new client has connected!");
				ClientHandler clientHandler = new ClientHandler(socket);

				Thread thread = new Thread (clientHandler);
				thread.start(); 
			}
		}
		catch (IOException e){}
	}
	public void closeServerSocker(){
		try{
			if(serverSocket != null){
				serverSocket.close();
			}
		}
		catch(IOException e){
			e.printStackTrace();
		}
	}
	public static void main (String [] args) throws IOException{
		ServerSocket serverSocket = new ServerSocket(1234);
		Server server = new Server (serverSocket);
		server.startServer();
	}
}

 3. Create the ClientHandler.java file, the rules apply to all, make sure that the name of the file is also similar to the class name.
    	The code:
    import java.io.*;
import java.net.*;
import java.util.ArrayList;

public class ClientHandler implements Runnable{

	public static ArrayList <ClientHandler> clientHandler = new ArrayList <> ();

	private Socket socket;
	private BufferedReader bufferedReader;
	private BufferedWriter bufferedWriter;
	private String clientUsername;

	public ClientHandler (Socket socket){
		try {
			this.socket = socket;
			this.bufferedWriter = new BufferedWriter (new OutputStreamWriter (socket.getOutputStream ()));
			this.bufferedReader = new BufferedReader (new InputStreamReader (socket.getInputStream()));
			this.clientUsername = bufferedReader.readLine();
			clientHandler.add(this);

			broadcastMessage ("SERVER:" + clientUsername + "has entered the chat!");
		}
		catch (IOException e){
			closeEverything (socket, bufferedReader, bufferedWriter);
		}
	}

	@Override
	public void run(){
		String messageFromClient;

		while (socket.isConnected()){
			try {
				messageFromClient = bufferedReader.readLine();
				broadcastMessage(messageFromClient);
			}
			catch (IOException e){
				closeEverything( socket, bufferedReader, bufferedWriter);
				break;
			}
		}
	}

	public void broadcastMessage(String messageToSend){
		for(ClientHandler clientHandler: clientHandler){
			try{
				if(!clientHandler.clientUsername.equals(clientUsername)){
					clientHandler.bufferedWriter.write(messageToSend);
					clientHandler.bufferedWriter.newLine();
					clientHandler.bufferedWriter.flush();
				}
			}
			catch(IOException e){
				closeEverything(socket, bufferedReader, bufferedWriter);
			}
		}
	}

	public void removeClientHandler(){
		clientHandler.remove(this);
		broadcastMessage("SERVER:" + clientUsername + "has left the chat!");
	}

	public void closeEverything( Socket socket, BufferedReader bufferedReader, BufferedWriter bufferedWriter){
		removeClientHandler();
		try{
			if(bufferedReader != null){
				bufferedReader.close();
			}
			if(bufferedWriter != null){
				bufferedWriter.close();
			}
			if(socket != null){
				socket.close();
			}
		}
		catch(IOException e){
			e.printStackTrace();
		}
	}
}

 5. Create the Client.java file.
 	The code: 
  import java.io.*;
import java.net.*;
import java.util.Scanner;

public class Client{
	private Socket socket;
	private BufferedReader bufferedReader;
	private BufferedWriter bufferedWriter;
	private String username;

	public Client(Socket socket, String username ){
		try{
			this.socket = socket;
			this.bufferedWriter = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
			this.bufferedReader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
			this.username = username;
		}
		catch (IOException e){
			closeEverything(socket, bufferedReader, bufferedWriter);
		}
	}

	public void sendMessage(){
		try{
			bufferedWriter.write(username);
			bufferedWriter.newLine();
			bufferedWriter.flush();

			Scanner scanner = new Scanner (System.in);
			while (socket.isConnected()){
				String messageToSend = scanner.nextLine();
				bufferedWriter.write(username+ ":" + messageToSend);
				bufferedWriter.newLine();
				bufferedWriter.flush();
			}
		}
		catch(IOException e){
			closeEverything(socket, bufferedReader, bufferedWriter);
		}
	}

	public void listenForMessage(){
		new Thread (new Runnable(){

			@Override
			public void run(){
				String msgFromGroupChat;

				while (socket.isConnected()){
					try{
						msgFromGroupChat = bufferedReader.readLine();
						System.out.println(msgFromGroupChat);
					}
					catch(IOException e){
						closeEverything(socket, bufferedReader, bufferedWriter);
					}
				}
			}
		}).start();
	}

	public void closeEverything(Socket socket, BufferedReader bufferedReader, BufferedWriter bufferedWriter){
		try{
			if (bufferedReader != null){
				bufferedReader.close();
			}
			if (bufferedWriter != null){
				bufferedWriter.close();
			}
			if (socket != null){
				socket.close();
			}
		}
		catch(IOException e){
			e.printStackTrace();
		}
	}
	public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Enter your username for the group chat:");
        String username = scanner.nextLine();
        try {
            Socket socket = new Socket("Localhost", 1234);
            Client client = new Client(socket, username);
            client.listenForMessage();
            client.sendMessage();
        } catch (UnknownHostException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

 6. Make sure that the files are saved with the .java extension.

# How to run the codes.
1. Open the command prompt on four different windows. This is beacuse one of them will be the server and the other three windows will be clients.
2. Resize them to fit on the screen.
3. On one window, run the Server file.
   	To run a hard coded java file, follow the following steps:
   		1. Open the files folder, go to the program files and click on the Java folder.
   		2. Open JDK and go to where the bin file is. Copy the path.
   		3. Go to the command prompt and type path = /copy paste the bin path/
   		4. Change the directory and copy paste the directory that has the JavaSocketProg folder.
   		5. To complie use javac.NameOfFile.java
   		6. To run use java.NameOfFile.
4. Do the same thing for all the Client windows.
5. You will be prompted to add user names and once added, it will reflect on the server that a user has connected.
6. The server will then broadcast the messages to all the clients except the one sending the message.

# HAPPY CODING By Abigail Vivian.
