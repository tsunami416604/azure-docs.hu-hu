---
title: A üzenetsor-tároló használata a Java-Azure Storage-ból
description: Megtudhatja, hogyan hozhat létre és törölhet várólistákat a Queue Storage használatával. Megtudhatja, hogyan szúrhat be, betekintést, beolvashat és törölhet üzeneteket a Javához készült Azure Storage ügyféloldali kódtár használatával.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 08/19/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 78e2bd80b689394b654a85ea913c618c08120091
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854010"
---
# <a name="how-to-use-queue-storage-from-java"></a>How to use Queue Storage from Java (A Queue Storage használata Javával)

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Ez az útmutató bemutatja, hogyan használható a gyakori forgatókönyvek kódja az Azure üzenetsor-tárolási szolgáltatással. A kódminták Java nyelven íródtak, és a [Java-hoz készült Azure Storage SDK-t][Azure Storage SDK for Java] használják. A forgatókönyvek közé tartoznak az üzenetsor-üzenetek **beszúrása**, **bepillantása**, **beolvasása**és **törlése** . A várólisták **létrehozásához** és **törléséhez** szükséges kód is szerepel. A várólistákkal kapcsolatos további információkért tekintse meg a [következő lépések](#next-steps) szakaszt.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása

# <a name="java-v12"></a>[Java V12](#tab/java)

Először is ellenőrizze, hogy a fejlesztői rendszer megfelel-e az [Azure üzenetsor Storage ügyféloldali kódtára Java V12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)-es verziójában felsorolt előfeltételeknek.

*Queues-How-to-V12*nevű Java-alkalmazás létrehozása:

1. A konzol ablakban (például cmd, PowerShell vagy bash) a Maven használatával hozzon létre egy új, a *Queues-to-V12*nevű konzol alkalmazást. A "Hello World!" létrehozásához írja be a következő **MVN** -parancsot: Java-projekt.

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

1. A projekt létrehozásának eredményét a következőhöz hasonló módon kell kinéznie:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *queues-howto-v12* directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a függőségek csoportjához a következő függőségi elemet.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[Java V8](#tab/java8)

Először is ellenőrizze, hogy a fejlesztői rendszer megfelel-e a Java V8-hoz készült [Azure Storage SDK](https://github.com/azure/azure-storage-java)-ban felsorolt előfeltételeknek. Kövesse a Javához készült Azure Storage-kódtárak letöltésére és telepítésére vonatkozó utasításokat. Ezután létrehozhat egy Java-alkalmazást a jelen cikk példái alapján.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása a várólista-tároló eléréséhez

Adja hozzá a következő importálási utasításokat azon Java-fájl elejéhez, amelyen az Azure Storage API-kat szeretné használni a várólisták eléréséhez:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage-beli kapcsolatok karakterláncának beállítása

Az Azure Storage-ügyfelek tárolási kapcsolatok sztringjét használják az adatkezelési szolgáltatások eléréséhez. Szerezze be a [Azure Portalban](https://portal.azure.com)felsorolt Storage-fiók nevét és elsődleges elérési kulcsát. Használja őket *accountname* és *AccountKey* értékként a kapcsolatok karakterláncában. Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```
Ezt a karakterláncot a *ServiceConfiguration. cscfg*nevű szolgáltatás-konfigurációs fájlban tárolhatja. Microsoft Azure szerepkörön belül futó alkalmazások esetén a **RoleEnvironment. getConfigurationSettings**meghívásával érheti el a kapcsolati karakterláncot. Íme egy példa arra, hogy a *StorageConnectionString*nevű **Setting** elemből hogyan kell beszerezni a kapcsolatok karakterláncát:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

A következő minták feltételezik, hogy van egy **karakterlánc** -objektum, amely tartalmazza a tárolási kapcsolatok karakterláncát.

## <a name="how-to-create-a-queue"></a>Útmutató: üzenetsor létrehozása

# <a name="java-v12"></a>[Java V12](#tab/java)

A **QueueClient** objektum tartalmazza a várólistával való interakció műveleteit. A következő kód létrehoz egy **QueueClient** objektumot. A használni kívánt várólista létrehozásához használja a **QueueClient** objektumot.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

A **CloudQueueClient** objektum lehetővé teszi a várólisták hivatkozási objektumainak lekérését. A következő kód létrehoz egy **CloudQueueClient** objektumot. (Megjegyzés: a **CloudStorageAccount** -objektumok létrehozásának további módjai is vannak; további tudnivalókért lásd: **CloudStorageAccount** az [Azure Storage ügyféloldali SDK-dokumentációjában].)

A **CloudQueueClient** objektum használatával kérjen egy hivatkozást a használni kívánt várólistára. Ha nem létezik, létrehozhat egy várólistát.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>Útmutató: üzenet hozzáadása egy várólistához

# <a name="java-v12"></a>[Java V12](#tab/java)

Ha egy üzenetet egy meglévő várólistába szeretne beszúrni, hívja meg a **üzenetküldés** metódust. Egy üzenet lehet karakterlánc (UTF-8 formátumban) vagy egy bájtos tömb. Az alábbi kód egy karakterlánc-üzenetet küld a várólistába.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Ha üzenetet szeretne beszúrni egy létező üzenetsorba, először hozzon létre egy **CloudQueueMessage** elemet. Ezután hívja meg a **addMessage** metódust. A **CloudQueueMessage** egy sztringből (UTF-8 formátumban) vagy egy bájttömbből hozható létre. Az alábbi kód létrehoz egy várólistát (ha nem létezik), és beszúrja a "Hello, World" üzenetet.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: betekintés a következő üzenetbe

A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistából a **peekMessage**meghívásával.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: várólistán lévő üzenet tartalmának módosítása

Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkahelyi feladatot jelöl, ezzel a funkcióval frissítheti az állapotot. A következő kód egy üzenetsor-üzenetet frissít új tartalommal, és beállítja a láthatósági időtúllépést további 30 másodperc kibővítéséhez. Ha kiterjeszti a láthatósági időtúllépést, a rendszer 30 másodpercet ad az ügyfélnek az üzenet használatának folytatásához. Az újrapróbálkozások száma is megtartható. Ha az üzenet többször is próbálkozik, mint *n* alkalommal, törölje azt. Ez a forgatókönyv olyan üzenetet véd, amely minden feldolgozáskor elindít egy alkalmazáshiba-hibát.

# <a name="java-v12"></a>[Java V12](#tab/java)

A következő mintakód az üzenetek várólistáján keres, megkeresi a keresési karakterláncnak megfelelő első üzenet tartalmát, módosítja az üzenet tartalmát, és kilép.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

A következő mintakód az üzenetek várólistáján keres, megkeresi a "Hello, World" karakterláncnak megfelelő első üzenet tartalmát, módosítja az üzenet tartalmát, és kilép.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

A következő mintakód csak az első látható üzenetet frissíti a várólistában.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>Útmutató: a várólista hosszának beolvasása

Megbecsülheti egy üzenetsorban található üzenetek számát.

# <a name="java-v12"></a>[Java V12](#tab/java)

A **getProperties** metódus több aktuális érték megadását kéri a Queue szolgáltatás. Az értékek egyike annak a száma, hogy hány üzenet van egy várólistában. A darabszám csak hozzávetőleges, mert az üzenetek a kérés után hozzáadhatók vagy eltávolíthatók. A **getApproximateMessageCount** metódus a **getProperties**hívása által lekért utolsó értéket adja vissza a Queue szolgáltatás hívása nélkül.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

A **downloadAttributes** metódus több aktuális érték megadását kéri a Queue szolgáltatás. Az értékek egyike annak a száma, hogy hány üzenet van egy várólistában. A darabszám csak hozzávetőleges, mert az üzenetek a kérés után hozzáadhatók vagy eltávolíthatók. A **getApproximateMessageCount** metódus a **downloadAttributes**hívása által lekért utolsó értéket adja vissza a Queue szolgáltatás hívása nélkül.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: a következő üzenet elküldése

# <a name="java-v12"></a>[Java V12](#tab/java)

A kód két lépésben elvégez egy üzenetsor-várólistán lévő üzenetet. A **receiveMessage**meghívásakor a következő üzenet jelenik meg egy várólistában. A **receiveMessage** által visszaadott üzenet láthatatlanná válik minden más, a várólistáról olvasó üzenetben. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez a **deleteMessage**is hívnia kell. Ha a kód nem tud feldolgozni egy üzenetet, ez a kétlépéses folyamat biztosítja, hogy ugyanazt az üzenetet kapja, és próbálkozzon újra. A kód a **deleteMessage** közvetlenül az üzenet feldolgozása után hívja meg.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

A kód két lépésben elvégez egy üzenetsor-várólistán lévő üzenetet. A **retrieveMessage**meghívásakor a következő üzenet jelenik meg egy várólistában. A **retrieveMessage** által visszaadott üzenet láthatatlanná válik minden más, a várólistáról olvasó üzenetben. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez a **deleteMessage**is hívnia kell. Ha a kód nem tud feldolgozni egy üzenetet, ez a kétlépéses folyamat biztosítja, hogy ugyanazt az üzenetet kapja, és próbálkozzon újra. A kód a **deleteMessage** közvetlenül az üzenet feldolgozása után hívja meg.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>További beállítások a dequeuing üzenetek számára

Az üzenetek várólistából való lekérését kétféleképpen lehet testre szabni. Először szerezzen be egy köteget az üzenetek közül (legfeljebb 32). Másodszor, állítsa be a hosszabb vagy rövidebb láthatósági időtúllépést, így a kód több vagy kevesebb időt is biztosít az egyes üzenetek teljes feldolgozásához.

# <a name="java-v12"></a>[Java V12](#tab/java)

A következő kódrészlet a **receiveMessages** metódus használatával 20 üzenetet kap egy hívásban. Ezután feldolgozza az egyes üzeneteket a **for** loop használatával. Azt is beállítja, hogy az egyes üzenetek esetében a láthatósági időkorlát 5 perc (300 másodperc) legyen. Az időtúllépés az összes üzenetnél egyszerre elindul. Ha öt perc telt el a **receiveMessages**hívása óta, a rendszer minden nem törölt üzenetet újra láthatóvá válik.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

A következő kódrészlet a **retrieveMessages** metódus használatával 20 üzenetet kap egy hívásban. Ezután feldolgozza az egyes üzeneteket a **for** loop használatával. Azt is beállítja, hogy az egyes üzenetek esetében a láthatósági időkorlát 5 perc (300 másodperc) legyen. Az időtúllépés az összes üzenetnél egyszerre elindul. Ha öt perc telt el a **retrieveMessages**hívása óta, a rendszer minden nem törölt üzenetet újra láthatóvá válik.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>Útmutató: a várólisták listázása

# <a name="java-v12"></a>[Java V12](#tab/java)

Az aktuális várólisták listájának beszerzéséhez hívja meg a **QueueServiceClient. listQueues ()** metódust, amely **várólistaelemhez** objektumok gyűjteményét fogja visszaadni.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Az aktuális várólisták listájának beszerzéséhez hívja meg a **CloudQueueClient. listQueues ()** metódust, amely **CloudQueue** objektumok gyűjteményét fogja visszaadni.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>Útmutató: üzenetsor törlése

# <a name="java-v12"></a>[Java V12](#tab/java)

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a **delete** metódust a **QueueClient** objektumon.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[Java V8](#tab/java8)

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a **deleteifexists paranccsal** metódust a **CloudQueue** objektumon.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a várólista-tárolás alapjait, az alábbi hivatkozásokat követve megismerheti az összetettebb tárolási feladatokat.

* [Javához készült Azure Storage SDK][Azure Storage SDK for Java]
* [Azure Storage ügyféloldali SDK-referencia][Azure Storage Client SDK Reference]
* [Az Azure Storage-szolgáltatások REST API-ja][Azure Storage Services REST API]
* [Az Azure Storage csapat blogja][Azure Storage Team Blog]

[Azure SDK for Java]: https://github.com/azure/azure-sdk-for-java
[Azure Storage SDK for Java]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage
[Azure Storage ügyféloldali SDK-referencia]: https://azure.github.io/azure-sdk-for-java/storage.html
[Azure Storage Services REST API]: https://docs.microsoft.com/rest/api/storageservices/
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
