---
title: Oktatóanyag – Kereskedelmi leltárválaszték frissítése közzétételi/előfizetési csatornák és témakörszűrők használatával az Azure CLI használatával | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan küldhet és fogadhat üzeneteket egy témakörből vagy előfizetésből, és miként adhat hozzá és használhat szűrési szabályokat az Azure parancssori felületről
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.author: sethm
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 42f0781de5412310ecb5326f0384268aba9c53dd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651469"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Oktatóanyag: Leltár frissítése parancssori felület és témakörök/előfizetések használatával

A Microsoft Azure Service Bus egy több-bérlős felhőalapú üzenetkezelési szolgáltatás, amely információkat küld alkalmazások és szolgáltatók között. Az aszinkron műveletek rugalmas, közvetítőalapú üzenettovábbítást, valamint strukturált, érkezési sorrendben történő üzenetkiküldést tesznek lehetővé. Emellett közzétételi vagy előfizetési lehetőségeket is biztosítanak. Ebből az oktatóanyagból megtudhatja, hogyan használhatók a Service Bus-témakörök és -előfizetések kereskedelmi leltárazási forgatókönyvekben az Azure parancssori felületet és Javát használó közzétételi/előfizetési csatornákkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Service Bus-témakör és egy vagy több hozzá tartozó előfizetés létrehozása az Azure parancssori felület használatával
> * Témakörszűrők hozzáadása az Azure parancssori felület használatával
> * Két különböző tartalmú üzenet létrehozása
> * Az üzenetek elküldése és annak ellenőrzése, hogy megérkeztek-e a megfelelő előfizetésekbe
> * Üzenet fogadása az előfizetésektől

Erre a forgatókönyvre egy példa, amikor több kiskereskedelmi üzletben frissül a leltárválaszték. Ebben az esetben minden egyes üzlet vagy üzletcsoport megkapja a neki szánt üzeneteket a választéka frissítéséről. Ez az oktatóanyag bemutatja, hogyan valósítható meg a forgatókönyv előfizetések és szűrők használatával. Először hozzon létre egy témakört 3 előfizetéssel, adjon hozzá szabályokat és szűrőket, majd küldjön és fogadjon üzeneteket a témaköröktől és az előfizetésektől.

![témakör](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes fiókot][].

## <a name="prerequisites"></a>Előfeltételek

Ha Service Bus-alkalmazást szeretne létrehozni a Java használatával, akkor a számítógépre a következőket kell telepíteni:

- [Java fejlesztői készlet](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), legújabb verzió.
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- Az [Apache Maven](https://maven.apache.org) 3.0-s vagy újabb verziója.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus-témakörök és -előfizetések

Minden egyes [témakörre való előfizetés](service-bus-messaging-overview.md#topics) másolatot kaphat az összes üzenetről. A témakörök teljes protokoll és szemantika szempontjából is teljesen kompatibilisek a Service Bus-üzenetsorokkal. A Service Bus-témakörök számos különféle kiválasztási szabályt támogatnak szűrőfeltételekkel, illetve az üzenet tulajdonságait beállító vagy módosító választható műveletekkel. Minden szabályegyezéskor létrejön egy üzenet. A szabályokról, szűrőkről és műveletekről ezen a [hivatkozáson](topic-filters.md) talál további információt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

A parancssori felület telepítése után nyisson meg egy parancssort, és a következő parancsok végrehajtásával jelentkezzen be az Azure-ba. Ezek a lépések nem szükségesek, ha Cloud Shellt használ:

1. Ha az Azure parancssori felületet helyileg használja, az alábbi parancs futtatásával jelentkezzen be az Azure-ba. Erre a bejelentkezési lépésre nincs szükség, ha ezeket a parancsokat a Cloud Shellben futtatja:

   ```azurecli-interactive
   az login
   ```

2. Állítsa az aktuális előfizetési környezetet a használni kívánt Azure-előfizetésre:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>A parancssori felület használata erőforrások kiosztásához

Hajtsa végre az alábbi parancsokat a Service Bus-erőforrások üzembe helyezéséhez. Ne felejtse el lecserélni az összes helyőrzőt a megfelelő értékre:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Az utolsó parancs futtatása után másolja ki és illessze be a kapcsolati sztringet és a kiválasztott üzenetsor nevét egy átmeneti helyre, például a Jegyzettömbbe. A következő lépésben szüksége lesz ezekre.

## <a name="create-filter-rules-on-subscriptions"></a>Szűrési szabályok létrehozása előfizetésekhez

Miután kiépítette a névteret és a témakört/előfizetéseket, valamint beszerezte a szükséges hitelesítő adatokat, készen áll az előfizetések szűrési szabályainak létrehozására, majd az üzenetek küldésére és fogadására. A kódot [ebben a GitHub-mintamappában](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/quickstarts-and-tutorials/tutorial-topics-subscriptions-filters-java/src/main/java/com/microsoft/azure/) vizsgálhatja meg.

## <a name="send-and-receive-messages"></a>Üzenetek küldése és fogadása

1. Győződjön meg róla, hogy a Cloud Shell nyitva van, és a Bash-parancssort jeleníti meg.

2. Klónozza a [Service Bus GitHub-adattárát](https://github.com/Azure/azure-service-bus/) a következő paranccsal:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Lépjen a következő mintamappához: `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`. Ne feledje, hogy a Bash-felületen a parancsok megkülönböztetik a kis- és nagybetűket, és útvonal-elválasztóként perjelet kell használni.

3. Az alkalmazás létrehozásához adja ki az alábbi parancsot:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. A program futtatásához adja ki az alábbi parancsot. Ne felejtse lecserélni a helyőrzőket az előző lépésben beszerzett kapcsolati sztringre és a témakör nevére:

   ```shell
  java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Figyeljen meg 10 üzenetet, amelyeket a rendszer elküld a témakörnek, majd az egyéni előfizetésektől fogadja őket:

   ![a program kimenete](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az alábbi paranccsal eltávolítható az erőforráscsoport, a névtér és az összes kapcsolódó erőforrás:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

Ez a szakasz a mintakód működésének további részleteit ismerteti.

### <a name="get-connection-string-and-queue"></a>A kapcsolati sztring és az üzenetsor lekérése

A kód először meghatározza azokat a változókat, amelyek a program további végrehajtását vezérlik:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";    
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

Csak a kapcsolati sztring és a témakör neve olyan érték, amelyet parancssori paramétereken keresztül ad hozzá, illetve ad át a `main()` metódusnak. A tényleges kódvégrehajtást a `run()` metódus indítja el, amely elküldi, majd fogadja az üzeneteket a témakörből:

```java
public static void main(String[] args) {
        TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
        // Send sample messages.
        this.sendMessagesToTopic();

        // Receive messages from subscriptions.
        this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Témakörügyfél létrehozása az üzenetek küldéséhez

Az üzenetek küldéséhez és fogadásához a `sendMessagesToTopic()` metódus létrehoz egy témakörügyfél-példányt, amely felhasználja a kapcsolati sztringet és a témakör nevét, majd meghív egy másik metódust az üzenetek elküldéséhez:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
         // Create client for the topic.
        TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

        // Create a message sender from the topic client.

        System.out.printf("\nSending orders to topic.\n");

        // Now we can start sending orders.
        CompletableFuture.allOf(
                SendOrders(topicClient,Store[0]),
                SendOrders(topicClient,Store[1]),
                SendOrders(topicClient,Store[2]),
                SendOrders(topicClient,Store[3]),
                SendOrders(topicClient,Store[4]),
                SendOrders(topicClient,Store[5]),
                SendOrders(topicClient,Store[6]),
                SendOrders(topicClient,Store[7]),
                SendOrders(topicClient,Store[8]),
                SendOrders(topicClient,Store[9])                
        ).join();

        System.out.printf("\nAll messages sent.\n");
    }

     public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));         
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8)); 
            // We always set the Sent to field
            message.setTo(store);    
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both. 
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});
                        
            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());            
            topicClient.sendAsync(message);
        }
               
        return new CompletableFuture().completedFuture(null);         
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Üzenetek fogadása az egyéni előfizetésekből

A `receiveAllMessages()` metódus meghívja a `receiveAllMessageFromSubscription()` metódust, amely ezután hívásonként létrehoz egy előfizetési ügyfelet, illetve fogadja az üzeneteket az egyéni előfizetésektől:

```java
public void receiveAllMessages() throws Exception {     
    System.out.printf("\nStart Receiving Messages.\n");
    
    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2]) 
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {
        
        int receivedMessages = 0;

        // Create subscription client.
        IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

        // Create a receiver from the subscription client and receive all messages.
        System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

        while (true)
        {
            // This will make the connection wait for N seconds if new messages are available. 
            // If no additional messages come we close the connection. This can also be used to realize long polling.
            // In case of long polling you would obviously set it more to e.g. 60 seconds.
            IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
            if (receivedMessage != null)
            {
                if ( receivedMessage.getProperties() != null ) {                                                                                
                    System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));                                                                                          
                    
                    // Show the label modified by the rule action
                    if(receivedMessage.getLabel() != null)
                        System.out.printf("Label=%s\n", receivedMessage.getLabel());   
                }
                
                byte[] body = receivedMessage.getBody();
                Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
                System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());                          
                
                subscriptionClient.complete(receivedMessage.getLockToken());
                receivedMessages++;
            }
            else
            {
                // No more messages to receive.
                subscriptionClient.close();
                break;
            }
        }
        System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
        
        return new CompletableFuture().completedFuture(null);
}
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban erőforrásokat osztott ki az Azure parancssori felület használatával, majd üzeneteket küldött és fogadott egy Service Bus-témakörből és annak előfizetéseiből. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Service Bus-témakör és egy vagy több hozzá tartozó előfizetés létrehozása az Azure Portal használatával
> * Témakörszűrők hozzáadása .NET-kód használatával
> * Két különböző tartalmú üzenet létrehozása
> * Az üzenetek elküldése és annak ellenőrzése, hogy megérkeztek-e a megfelelő előfizetésekbe
> * Üzenet fogadása az előfizetésektől

Az üzenetküldéssel és -fogadással kapcsolatos további példákért tekintse meg a [Service Bus-mintákat a GitHubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Folytassa a következő oktatóanyaggal, ha szeretne többet megtudni a Service Bus közzétételi/előfizetési funkcióiról.

> [!div class="nextstepaction"]
> [Leltár frissítése a PowerShell és témakörök/előfizetések használatával](service-bus-tutorial-topics-subscriptions-portal.md)

[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install Azure CLI 2.0]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create