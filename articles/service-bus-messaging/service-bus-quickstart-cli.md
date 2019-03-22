---
title: Rövid útmutató – használja az Azure CLI használatával hozzon létre egy Service Bus-üzenetsorba |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan hozhat létre a Service Bus-üzenetsorba a az Azure CLI használatával. Ezt követően használhatja egy Java-mintaalkalmazás, üzenetek küldéséhez és üzenetek fogadása az üzenetsorból.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: java
ms.topic: quickstart
ms.date: 02/25/2019
ms.author: spelluru
ms.openlocfilehash: b40cd130039f7d55b69198b4be4af908dedbe8b7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076887"
---
# <a name="quickstart-use-the-azure-cli-to-create-a-service-bus-queue"></a>Gyors útmutató: Az Azure CLI használatával hozzon létre egy Service Bus-üzenetsorba

A Microsoft Azure Service Bus egy vállalati integrációs üzenetközvetítő, amely biztonságos üzenetküldést és megbízható szolgáltatást nyújt. Egy jellemző Service Bus-forgatókönyv általában két vagy több alkalmazás, szolgáltatás vagy folyamat egymástól való szétválasztását (az alkalmazásoknak nem kell egyidejűleg online állapotban lenniük), az állapot- vagy adatváltozások továbbítását, valamint az alkalmazások közötti üzenetküldést foglalja magában. 

Például egy kereskedelmi vállalat elküldheti az értékesítési pontokra vonatkozó adatait egy háttérrendszernek vagy egy regionális terjesztőközpontnak feltöltés vagy leltárfrissítés céljából. Ebben az esetben az ügyfélalkalmazás üzeneteket küld és fogad egy Service Bus-üzenetsorból:

![üzenetsor](./media/service-bus-quickstart-cli/quick-start-queue.png)

Ebben a rövid útmutató üzenetek küldése és fogadása Service Bus szolgáltatással az Azure CLI és a Service Bus-Java-kódtár használatával. Ha további technikai részletekre kíváncsi, olvassa el a mintakód fő elemeihez tartozó [magyarázatot](#understand-the-sample-code).

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes fiókot][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az Azure Portal jobb felső sarkában található menüben kattintson a Cloud Shell gombra, majd a **Környezet kiválasztása** legördülő menüben válassza a **Bash** lehetőséget. 

## <a name="use-the-azure-cli-to-create-resources"></a>Erőforrások létrehozása az Azure CLI használatával

A Cloud Shellben a Bash parancssorból indítsa el az alábbi parancsokat a Service Bus-erőforrások kiépítéséhez. Ne felejtse el lecserélni az összes helyőrzőt a megfelelő értékre: A Java-sample program vár BasicQueue, így nem módosítani kell az üzenetsor neve. 

```azurecli-interactive
# Create a resource group
resourceGroupName="myResourceGroup"

az group create --name $resourceGroupName --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create --resource-group $resourceGroupName --name $namespaceName --location eastus

# Create a Service Bus queue
az servicebus queue create --resource-group $resourceGroupName --namespace-name $namespaceName --name BasicQueue

# Get the connection string for the namespace
connectionString=$(az servicebus namespace authorization-rule keys list --resource-group $resourceGroupName --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv)
```

Az utolsó parancs futtatása után másolja ki és illessze be a kapcsolati sztringet és a kiválasztott üzenetsor nevét egy átmeneti helyre, például a Jegyzettömbbe. A következő lépésben szüksége lesz ezekre.

## <a name="send-and-receive-messages"></a>Üzenetek küldése és fogadása

Miután létrehozta a névteret és az üzenetsort, valamint beszerezte a szükséges hitelesítő adatokat, készen áll az üzenetek küldésére és fogadására. A kódot [ebben a GitHub-mintamappában](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters) vizsgálhatja meg.

1. Klónozás a [Service Bus GitHub-adattár](https://github.com/Azure/azure-service-bus/) a számítógépen a következő parancsot:

   ```bash
   git clone https://github.com/Azure/azure-service-bus.git
   ```

1. Módosítsa az aktuális könyvtárat a mintamappára, útvonal-elválasztóként pedig használjon perjelet:

   ```bash
   cd azure-service-bus/samples/Java/azure-servicebus/QueuesGettingStarted
   ```

1. Az alkalmazás létrehozásához adja ki az alábbi parancsot:
   
   ```bash
   mvn clean package -DskipTests
   ```

1. Futtassa a programot, és cserélje le a kapcsolati karakterláncot a korábban kimásolt érték után a következő parancs kiadásával:

   ```bash
   java -jar ./target/queuesgettingstarted-1.0.0-jar-with-dependencies.jar -c "<SERVICE BUS NAMESPACE CONNECTION STRING>" 
   ```

1. Figyelje meg 10 üzenet küldését az üzenetsorban. Az üzenetek rendezése nem garantált, de az üzeneteket küldi el, majd arra vonatkozik, és kapott, a hasznos adatok mellett látható:

    ```
    Message sending: Id = 0
    Message sending: Id = 1
    Message sending: Id = 2
    Message sending: Id = 3
    Message sending: Id = 4
    Message sending: Id = 5
    Message sending: Id = 6
    Message sending: Id = 7
    Message sending: Id = 8
    Message sending: Id = 9
            Message acknowledged: Id = 9
            Message acknowledged: Id = 3
                                    Message received:
                                                    MessageId = 9,
                                                    SequenceNumber = 54324670505156609,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Nikolaus, name = Kopernikus ]
    
            Message acknowledged: Id = 2
            Message acknowledged: Id = 5
            Message acknowledged: Id = 1
            Message acknowledged: Id = 8
            Message acknowledged: Id = 7
            Message acknowledged: Id = 0
            Message acknowledged: Id = 6
            Message acknowledged: Id = 4
                                    Message received:
                                                    MessageId = 3,
                                                    SequenceNumber = 58828270132527105,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.972Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.972Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Steven, name = Hawking ]
    
                                    Message received:
                                                    MessageId = 2,
                                                    SequenceNumber = 9288674231451649,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.012Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.012Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Marie, name = Curie ]
    
                                    Message received:
                                                    MessageId = 1,
                                                    SequenceNumber = 22799473113563137,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.025Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.025Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Werner, name = Heisenberg ]
    
                                    Message received:
                                                    MessageId = 8,
                                                    SequenceNumber = 67835469387268097,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.028Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.028Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Johannes, name = Kepler ]
    
                                    Message received:
                                                    MessageId = 7,
                                                    SequenceNumber = 4785074604081153,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.020Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.020Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Galileo, name = Galilei ]
    
                                    Message received:
                                                    MessageId = 5,
                                                    SequenceNumber = 13792273858822145,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.027Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.027Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Niels, name = Bohr ]
    
                                    Message received:
                                                    MessageId = 0,
                                                    SequenceNumber = 18295873486192641,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.021Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.021Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Albert, name = Einstein ]
    
                                    Message received:
                                                    MessageId = 6,
                                                    SequenceNumber = 281474976710657,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:21.019Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:21.019Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Michael, name = Faraday ]
    
                                    Message received:
                                                    MessageId = 4,
                                                    SequenceNumber = 63331869759897601,
                                                    EnqueuedTimeUtc = 2019-02-25T18:15:20.964Z,
                                                    ExpiresAtUtc = 2019-02-25T18:17:20.964Z,
                                                    ContentType = "application/json",
                                                    Content: [ firstName = Isaac, name = Newton ]
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure Cloud shellben futtassa az alábbi paranccsal eltávolítható az erőforráscsoport, a névtér és az összes kapcsolódó erőforrás:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

Ez a szakasz a mintakód fő szakaszaival kapcsolatos további részleteket ismerteti. A GitHub-adattárban található kódot megkeresheti [itt](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

### <a name="get-connection-string"></a>Kapcsolati sztring lekérése

A AlkalmazásFuttatása módszer beolvassa a kapcsolati karakterlánc értékét az argumentumok a programba. 

```java
public static void main(String[] args) {

    System.exit(runApp(args, (connectionString) -> {
        QueuesGettingStarted app = new QueuesGettingStarted();
        try {
            app.run(connectionString);
            return 0;
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 1;
        }
    }));
}

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
```

### <a name="create-queue-clients-to-send-and-receive"></a>Üzenetsor-ügyfelek létrehozása a küldéshez és fogadáshoz

A `run()` metódus az üzenetek küldéséhez és fogadásához üzenetsorügyfél-példányokat hoz létre, amelyeket a kapcsolati sztringből és az üzenetsor nevéből állít össze. Ez a kód két üzenetsorügyfelet hoz lére, egyet a küldéshez és egyet a fogadáshoz:

```java
public void run(String connectionString) throws Exception {

    // Create a QueueClient instance for receiving using the connection string builder
    // We set the receive mode to "PeekLock", meaning the message is delivered
    // under a lock and must be acknowledged ("completed") to be removed from the queue
    QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    // We are using single thread executor as we are only processing one message at a time
    ExecutorService executorService = Executors.newSingleThreadExecutor();
    this.registerReceiver(receiveClient, executorService);

    // Create a QueueClient instance for sending and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(connectionString, "BasicQueue"), ReceiveMode.PEEKLOCK);
    this.sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    // wait for ENTER or 10 seconds elapsing
    waitForEnter(10);

    // shut down receiver to close the receive loop
    receiveClient.close();
    executorService.shutdown();
}
``` 

### <a name="construct-and-send-messages"></a>Üzenetek létrehozása és küldése

A `sendMessagesAsync()` metódus egy 10 üzenetből álló készletet hoz létre, és aszinkron módon elküldi őket az üzenetsorügyféllel:

```java
CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
    List<HashMap<String, String>> data =
            GSON.fromJson(
                    "[" +
                            "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                            "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                            "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                            "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                            "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                            "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                            "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                            "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                            "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                            "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                            "]",
                    new TypeToken<List<HashMap<String, String>>>() {}.getType());

    List<CompletableFuture> tasks = new ArrayList<>();
    for (int i = 0; i < data.size(); i++) {
        final String messageId = Integer.toString(i);
        Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
        message.setContentType("application/json");
        message.setLabel("Scientist");
        message.setMessageId(messageId);
        message.setTimeToLive(Duration.ofMinutes(2));
        System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
        tasks.add(
                sendClient.sendAsync(message).thenRunAsync(() -> {
                    System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                }));
    }
    return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
}
```

### <a name="receive-messages"></a>Üzenetek fogadása

A `registerReceiver()` metódus regisztrálja a `RegisterMessageHandler` visszahívását, és megad néhány üzenetkezelői beállítást:

```java
void registerReceiver(QueueClient queueClient, ExecutorService executorService) throws Exception {

    
    // register the RegisterMessageHandler callback with executor service
    queueClient.registerMessageHandler(new IMessageHandler() {
                                            // callback invoked when the message handler loop has obtained a message
                                            public CompletableFuture<Void> onMessageAsync(IMessage message) {
                                                // receives message is passed to callback
                                                if (message.getLabel() != null &&
                                                        message.getContentType() != null &&
                                                        message.getLabel().contentEquals("Scientist") &&
                                                        message.getContentType().contentEquals("application/json")) {

                                                    byte[] body = message.getBody();
                                                    Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                                                    System.out.printf(
                                                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                                                    "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                                                            message.getMessageId(),
                                                            message.getSequenceNumber(),
                                                            message.getEnqueuedTimeUtc(),
                                                            message.getExpiresAtUtc(),
                                                            message.getContentType(),
                                                            scientist != null ? scientist.get("firstName") : "",
                                                            scientist != null ? scientist.get("name") : "");
                                                }
                                                return CompletableFuture.completedFuture(null);
                                            }

                                            // callback invoked when the message handler has an exception to report
                                            public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                                                System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                                            }
                                        },
            // 1 concurrent call, messages are auto-completed, auto-renew duration
            new MessageHandlerOptions(1, true, Duration.ofMinutes(1)),
            executorService);

}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Service Bus-névteret és az üzenetsorba történő üzenetküldéshez és -fogadáshoz szükséges egyéb erőforrásokat hozott létre. Ha többet szeretne megtudni az üzenetfogadásra és -küldésre szolgáló kódok írásáról, lépjen tovább a Service Busról szóló oktatóanyagra:

> [!div class="nextstepaction"]
> [Leltár frissítése az Azure CLI-vel és a Java használatával](./service-bus-tutorial-topics-subscriptions-cli.md)

[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
