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

 
