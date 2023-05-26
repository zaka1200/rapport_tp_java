# rapport_tp_java

![image](https://github.com/zaka1200/rapport_tp_java/assets/121964432/3c70e488-7bc7-4840-b569-53e502549d4d)

# introduction :

L'objectif de ce rapport est de présenter le développement d'une application client-serveur permettant de calculer les factorielles d'un nombre donné. Cette application offre la possibilité aux utilisateurs de se connecter en tant que client et d'envoyer des valeurs au serveur pour effectuer les calculs. Le serveur reçoit les valeurs, effectue les calculs des factorielles correspondantes, puis renvoie les résultats au client. L'interaction entre le client et le serveur se fait de manière fiable et bidirectionnelle grâce à l'utilisation de sockets TCP/IP.

Ce projet met en évidence l'implémentation d'une architecture client-serveur dans le contexte spécifique du calcul des factorielles. L'architecture client-serveur est couramment utilisée pour distribuer efficacement les tâches et faciliter l'interaction entre les utilisateurs et les ressources disponibles sur un réseau. En utilisant cette architecture, les utilisateurs peuvent externaliser le calcul des factorielles vers un serveur distant, ce qui présente des avantages en termes de puissance de calcul et de performance.

# Scénario 1 : Le client envoie une seule valeur au serveur afin de calculer son factorielle

Dans ce premier scénario, le client établit une connexion avec le serveur et envoie une valeur unique pour calculer sa factorielle. Une fois connecté, le client saisit la valeur souhaitée à travers l'interface du terminal. Le serveur reçoit ensuite cette valeur, effectue le calcul de la factorielle correspondante, puis renvoie le résultat au client. Enfin, le client affiche le résultat dans son propre interface utilisateur. Ainsi, le client et le serveur interagissent de manière à ce que le client puisse obtenir le résultat du calcul de la factorielle pour la valeur qu'il a envoyée.

## code client :

```java
package tp1tcp;

//A Java program for a Client

import java.io.*;
import java.net.*;

public class client {
    public static void main(String[] args) {
        try {
            Socket socket = new Socket("localhost", 2000);

            // Envoi de la valeur au serveur
            int valueToSend = 10;
            OutputStream outputStream = socket.getOutputStream();
            ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
            objectOutputStream.writeObject(valueToSend);

            // Réception de la valeur renvoyée par le serveur
            InputStream inputStream = socket.getInputStream();
            ObjectInputStream objectInputStream = new ObjectInputStream(inputStream);
            int receivedValue = (int) objectInputStream.readObject();
            
            
            
            System.out.println("Received value from server: " + receivedValue);

            // Fermeture des flux et de la connexion
            objectOutputStream.close();
            objectInputStream.close();
            socket.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```

# explication du code client :


````java
import java.io.*;
import java.net.*;
````

Ces lignes importent les classes nécessaires pour la communication via des flux d'entrée/sortie (java.io.*) et l'utilisation de sockets pour la communication réseau (java.net.*).

````java
public class client {
    public static void main(String[] args) {

try {
    Socket socket = new Socket("localhost", 2000);
    
````

Ce bloc try crée un objet Socket en se connectant au serveur sur l'adresse localhost (l'adresse IP de la machine locale) et sur le port 2000.


````java
int valueToSend = 10;
OutputStream outputStream = socket.getOutputStream();
ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
objectOutputStream.writeObject(valueToSend);
````

Le client prépare une valeur à envoyer au serveur. Dans cet exemple, la valeur est définie comme 10. Le client récupère un flux de sortie à partir du Socket et l'utilise pour créer un ObjectOutputStream, qui permet d'écrire des objets dans le flux de sortie. Ensuite, le client écrit la valeur dans le flux de sortie avec writeObject().

```java
InputStream inputStream = socket.getInputStream();
ObjectInputStream objectInputStream = new ObjectInputStream(inputStream);
int receivedValue = (int) objectInputStream.readObject();
````

Le client récupère un flux d'entrée à partir du Socket et l'utilise pour créer un ObjectInputStream, qui permet de lire des objets à partir du flux d'entrée. Ensuite, le client lit un objet à partir du flux (readObject()) et le cast en un entier (int). Cette valeur représente le résultat renvoyé par le serveur.

````java
System.out.println("Received value from server: " + receivedValue);
````

Le client affiche la valeur reçue du serveur.

```java
objectOutputStream.close();
objectInputStream.close();
socket.close();
```

Ces lignes ferment les flux (objectOutputStream et objectInputStream), ainsi que le socket (socket). Cela libère les ressources utilisées par la connexion.

```java
} catch (Exception e) {
    e.printStackTrace();
}

```

Ce bloc catch capture toutes les exceptions qui pourraient être levées lors de l'exécution du code et imprime la trace de la pile d'erreur.

C'est ainsi que le code du client fonctionne, en se connectant au serveur, envoyant une valeur, recevant le résultat du serveur et affichant la valeur reçue.

# code serveur :

````java
package tp1tcp;

import java.io.*;
import java.net.*;

public class server {
    public static void main(String[] args) {
        try {
            ServerSocket serverSocket = new ServerSocket(2000);
            System.out.println("Server started. Waiting for connections...");

            Socket socket = serverSocket.accept();
            System.out.println("Client connected.");

            // Réception de la valeur envoyée par le client
            InputStream inputStream = socket.getInputStream();
            ObjectInputStream objectInputStream = new ObjectInputStream(inputStream);
            int receivedValue = (int) objectInputStream.readObject();
            
            
            System.out.println("Received value from client: " + receivedValue);

            // Calcul du factoriel de la valeur
            
            
            int factorial = calculateFactorial(receivedValue);

            // Envoi du résultat au client
            OutputStream outputStream = socket.getOutputStream();
            ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
            objectOutputStream.writeObject(factorial);
            System.out.println("Factorial of " + receivedValue + " calculated and sent to client: " + factorial);

            // Fermeture des flux et de la connexion
            objectInputStream.close();
            objectOutputStream.close();
            socket.close();
            serverSocket.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static int calculateFactorial(int n) {
        if (n == 0) {
            return 1;
        } else {
            return n * calculateFactorial(n - 1);
        }
    }
}
````

# explication code  serveur :

Ce code représente un serveur TCP qui reçoit une valeur entière de la part d'un client, calcule le factoriel de cette valeur, puis envoie le résultat au client.

- La classe s'appelle server et contient une méthode main qui est le point d'entrée du programme.

- La première étape dans le bloc try est de créer un socket serveur (ServerSocket) en spécifiant le port 2000 sur lequel le serveur écoutera les connexions entrantes.

- Une fois le socket serveur créé, le serveur attend une connexion entrante en appelant la méthode accept() sur le socket serveur. Cette méthode bloque l'exécution du programme jusqu'à ce qu'une connexion soit établie avec un client.

- Une fois la connexion établie, le serveur affiche un message pour indiquer que le client est connecté.

- Le serveur commence alors à recevoir la valeur envoyée par le client. Pour cela, il crée un flux d'entrée (InputStream) à partir du socket et le passe à un ObjectInputStream pour faciliter la lecture de l'objet envoyé par le client. L'objet reçu est converti en un entier (int) représentant la valeur.

- Le serveur affiche la valeur reçue du client.

- Ensuite, le serveur calcule le factoriel de la valeur reçue en appelant la méthode calculateFactorial().

- Après avoir calculé le factoriel, le serveur envoie le résultat au client. Il crée un flux de sortie (OutputStream) à partir du socket et le passe à un ObjectOutputStream pour faciliter l'écriture de l'objet envoyé au client. Le factoriel calculé est écrit dans le flux de sortie.

- Le serveur affiche un message indiquant que le factoriel de la valeur reçue a été calculé et envoyé au client.

- Une fois l'envoi terminé, le serveur ferme les flux (objectInputStream et objectOutputStream) et le socket client. Ensuite, il ferme également le socket serveur pour libérer les ressources utilisées.

- Si une exception se produit à n'importe quelle étape, elle est capturée par le bloc catch et une trace de la pile d'erreur est affichée.

- La classe contient également une méthode privée calculateFactorial() qui prend un entier n et calcule récursivement le factoriel de n. Cette méthode est utilisée pour effectuer le calcul dans le serveur.

Ce code représente un serveur simple qui peut recevoir une valeur du client, effectuer un calcul sur cette valeur et renvoyer le résultat.

# test du sen 1 :

The client will establish a connection with the server and send a value (in this case, 7) for calculating the factorial. The server will receive the value, compute the factorial, and send the result back to the client. The client will display the received factorial value on its interface, confirming the successful communication and calculation

- the result needs to be  : 3628800

![image](https://github.com/zaka1200/rapport_tp_java/assets/121964432/670e116d-5efa-4649-b61d-c35ed124c1c7)

# Scenario 2: Client envoie plusieurs valeurs au serveur jusqu'à "quit" :

Dans ce deuxième scénario, le client établit une connexion avec le serveur et envoie plusieurs valeurs successives pour calculer leurs factorielles. Le client continuera d'envoyer les valeurs jusqu'à ce qu'il entre le mot-clé "quit" ou q pour indiquer la fin de l'envoi. À chaque valeur envoyée, le serveur recevra la valeur, effectuera le calcul de la factorielle correspondante et renverra le résultat au client. Le client affichera ensuite le résultat dans son interface utilisateur. Ce scénario permet au client d'effectuer plusieurs calculs de factorielles successifs en une seule session, offrant ainsi une plus grande flexibilité dans l'utilisation de l'application.

# code serveur : 

```java
import java.io.*;
import java.net.*;

public class Server {
    public static void main(String[] args) {
        try {
            ServerSocket serverSocket = new ServerSocket(2000);
            System.out.println("Serveur en attente de connexions...");

            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("Nouvelle connexion acceptée.");

                ClientHandler clientHandler = new ClientHandler(clientSocket);
                Thread thread = new Thread(clientHandler);
                thread.start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    static class ClientHandler implements Runnable {
        private Socket clientSocket;

        public ClientHandler(Socket socket) {
            this.clientSocket = socket;
        }

        @Override
        public void run() {
            try {
                BufferedReader input = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                PrintWriter output = new PrintWriter(clientSocket.getOutputStream(), true);

                String inputLine;
                while ((inputLine = input.readLine()) != null) {
                    try {
                        int number = Integer.parseInt(inputLine);
                        if (number >= 0) {
                            long factorial = calculateFactorial(number);
                            output.println(factorial);
                        } else {
                            output.println("Invalid input: Please enter a positive integer.");
                        }
                    } catch (NumberFormatException e) {
                        output.println("Invalid input: Please enter a valid integer.");
                    }
                }

                input.close();
                output.close();
                clientSocket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        private long calculateFactorial(int number) {
            long factorial = 1;
            for (int i = 1; i <= number; i++) {
                factorial *= i;
            }
            return factorial;
        }
    }
}

```

# explication code serveur :

le code représente un serveur TCP qui écoute sur le port 2000. Il accepte les connexions entrantes des clients et gère chaque connexion dans un thread séparé.

La classe Server contient la méthode main, qui est le point d'entrée de l'application. Elle crée une instance de ServerSocket qui écoute sur le port 1234, puis entre dans une boucle infinie.

Dans la boucle, le serveur attend de nouvelles connexions à l'aide de serverSocket.accept(). Lorsqu'une nouvelle connexion est établie, un message est affiché pour indiquer qu'une nouvelle connexion a été acceptée.

Ensuite, un objet ClientHandler est créé pour gérer la communication avec le client. Un thread est ensuite démarré pour exécuter le ClientHandler.

La classe interne ClientHandler implémente l'interface Runnable, ce qui permet d'exécuter le code de gestion du client dans un thread séparé. Dans la méthode run(), elle récupère les flux d'entrée et de sortie du client à l'aide desquels elle communique.

Le code lit les lignes envoyées par le client à l'aide d'un BufferedReader et les traite tant qu'elles ne sont pas nulles. Chaque ligne est convertie en entier et vérifiée pour s'assurer qu'elle est positive. Si c'est le cas, le facteur est calculé à l'aide de la méthode calculateFactorial() et renvoyé au client via le PrintWriter. Si la ligne n'est pas un entier valide ou si elle est négative, un message d'erreur approprié est renvoyé au client.

Une fois que la boucle de lecture est terminée, les flux et la connexion avec le client sont fermés.

La méthode calculateFactorial() calcule le factoriel d'un nombre donné en utilisant une boucle simple.

En résumé, ce code implémente un serveur TCP capable de gérer plusieurs connexions clientes simultanées. Chaque client peut envoyer des valeurs au serveur pour calculer leurs factorielles, et le serveur renvoie les résultats correspondants au client.

# code client :

```java
import java.io.*;
import java.net.*;

public class Client {
    public static void main(String[] args) {
        try {
            Socket socket = new Socket("127.0.0.1", 2000);
            BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
            BufferedReader serverInput = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            PrintWriter output = new PrintWriter(socket.getOutputStream(), true);

            System.out.println("=== Factorial Calculator ===");

            while (true) {
                System.out.print("Enter a positive integer (or 'q' to quit): ");
                String userInput = input.readLine();
                if (userInput.equalsIgnoreCase("q")) {
                    break;
                }

                output.println(userInput);
                String serverResponse = serverInput.readLine();
                System.out.println("Factorial: " + serverResponse);
            }

            input.close();
            serverInput.close();
            output.close();
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

# explication code client :

Ce code représente un client TCP qui se connecte à un serveur à l'aide de l'adresse "localhost" et du port 2000. Le client permet à l'utilisateur d'entrer des entiers positifs pour calculer leurs factorielles en interagissant avec le serveur.

La méthode `main` de la classe `Client` est le point d'entrée de l'application. Elle commence par établir une connexion avec le serveur en créant un objet `Socket`.

Ensuite, les flux d'entrée et de sortie du client sont initialisés. `BufferedReader input` est utilisé pour lire les entrées de l'utilisateur à partir de la console, `BufferedReader serverInput` est utilisé pour lire les réponses du serveur et `PrintWriter output` est utilisé pour envoyer les entrées de l'utilisateur au serveur.

Le programme affiche ensuite un message d'accueil pour l'utilisateur.

Une boucle infinie est utilisée pour permettre à l'utilisateur d'entrer plusieurs valeurs pour calculer leurs factorielles. À chaque itération de la boucle, l'utilisateur est invité à entrer un entier positif à partir de la console. S'il entre "q", la boucle est interrompue et le programme se termine.

La valeur entrée par l'utilisateur est envoyée au serveur via `output.println(userInput)`. Ensuite, le client attend la réponse du serveur en lisant une ligne de `serverInput.readLine()`. Cette réponse est affichée à l'utilisateur sous la forme "Factorial: <serverResponse>".

Une fois que l'utilisateur a terminé d'entrer les valeurs, tous les flux et la connexion avec le serveur sont fermés.

En résumé, ce code représente un client TCP qui permet à l'utilisateur d'envoyer des valeurs au serveur pour calculer leurs factorielles. Le client affiche les résultats renvoyés par le serveur.


# les ameliorations ajoutees :
  
- Gestion des erreurs : La gestion des erreurs a été ajoutée au code du serveur en utilisant des blocs try-catch. Il capture une NumberFormatException si le client envoie une entrée entière invalide. Dans de tels cas, un message d'erreur approprié est renvoyé au client.

- Validation des entrées : Le code du serveur vérifie maintenant si le nombre reçu est non négatif. Si le nombre est négatif, un message d'erreur est renvoyé au client. Cela valide l'entrée de l'utilisateur du côté du serveur.

- Demandes multiples : Le code du client permet maintenant à l'utilisateur d'envoyer plusieurs demandes de calcul de factorielles sans arrêter le programme. L'utilisateur peut entrer 'q' pour quitter le programme.

- Traitement simultané : Le code du serveur reste inchangé par rapport à la version précédente, mais vous pouvez l'améliorer en mettant en œuvre un pool de threads ou un service d'exécution pour

# test du sen 2 :
  
  Vous pouvez maintenant entrer des entiers positifs dans le terminal du client pour calculer leurs factorielles. Après chaque entrée, le résultat de la factorielle sera affiché. Vous pouvez entrer plusieurs valeurs à la suite sans quitter le programme en entrant 'q' pour terminer.
  
  ![image](https://github.com/zaka1200/rapport_tp_java/assets/121964432/a32f2bd9-ac85-43d5-a915-6fe5773932e6)

# conclusion :
  
  En conclusion, le système client-serveur développé pour le calcul du factoriel a été implémenté avec succès. L'application offre la possibilité à un client de se connecter au serveur, d'envoyer une valeur pour le calcul de son factoriel, puis de recevoir le résultat calculé renvoyé par le serveur. La communication entre le client et le serveur s'effectue de manière efficace grâce à l'utilisation de flux d'entrée/sortie d'objets, permettant ainsi un échange de données fluide et fiable. Le système fonctionne de manière fiable, offrant aux utilisateurs la possibilité de déléguer le calcul des factoriels à un serveur distant, ce qui peut améliorer les performances et la puissance de calcul.
