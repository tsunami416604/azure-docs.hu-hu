---
title: A üzenetsor-tároló használata a Java-Azure Storage-ból
description: Megtudhatja, hogyan hozhat létre és törölhet várólistákat az üzenetsor-tárolóval, illetve hogyan szúrhat be, kérhet le és törölhet üzeneteket a Javához készült Azure Storage ügyféloldali kódtár használatával.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: a0c94f3c9af9220bb3cf6476c50799d1f3313ffc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809246"
---
# <a name="how-to-use-queue-storage-from-java"></a>How to use Queue Storage from Java (A Queue Storage használata Javával)

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure üzenetsor-tárolási szolgáltatás használatával. A kódminták Java nyelven íródtak, és a [Java-hoz készült Azure Storage SDK-t][Azure Storage SDK for Java] használják. A tárgyalt forgatókönyvek közé tartozik például a várólista-üzenetek **beszúrása**, **bepillantása**, **beolvasása**és **törlése** , valamint a várólisták **létrehozása** és **törlése** . A várólistákkal kapcsolatos további információkért tekintse meg a [következő lépések](#next-steps) szakaszt.

> [!IMPORTANT]
> Ez a cikk a Javához készült Azure Storage ügyféloldali kódtár örökölt verziójára vonatkozik. A legújabb verzió használatának megkezdéséhez tekintse meg a következőt: gyors útmutató [: Azure üzenetsor Storage ügyféloldali kódtár Java](storage-quickstart-queues-java.md) -hoz

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása

Ebben az útmutatóban olyan tárolási szolgáltatásokat fog használni, amelyek egy Java-alkalmazásban helyileg, vagy egy Azure-beli webalkalmazásban futó kódban futtathatók.

Ehhez telepítenie kell a Java Development Kit-t (JDK), és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésében. Miután végzett, ellenőriznie kell, hogy a fejlesztői rendszer megfelel-e a GitHubon a Java-tárházhoz készült [Azure Storage SDK][Azure Storage SDK for Java] -ban felsorolt minimális követelményeknek és függőségeknek. Ha a rendszer megfelel ezeknek a követelményeknek, kövesse a Java rendszerhez készült Azure Storage-kódtárak letöltésére és telepítésére vonatkozó utasításokat az adott adattárból. A feladatok elvégzése után létrehozhat egy Java-alkalmazást, amely a jelen cikkben szereplő példákat használja.

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása a várólista-tároló eléréséhez

Adja hozzá a következő importálási utasításokat azon Java-fájl elejéhez, amelyen az Azure Storage API-kat szeretné használni a várólisták eléréséhez:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage-beli kapcsolatok karakterláncának beállítása

Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ügyfélalkalmazásban történő futtatáskor a tárolási kapcsolati sztringet a következő formátumban kell megadnia: Használja a Storage-fiók nevét és az [Azure Portalon](https://portal.azure.com) megtalálható elsődleges hozzáférési kulcsát az *AccountName* és *AccountKey* értékeként. Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

A Microsoft Azure egyik szerepkörén belül futó alkalmazásokban ez a karakterlánc a szolgáltatás konfigurációs fájljában ( *ServiceConfiguration. cscfg)* tárolható, és a **RoleEnvironment. getConfigurationSettings** metódus hívásával érhető el. A következő példa bemutatja, hogyan kérhető le egy kapcsolati sztring egy *StorageConnectionString* nevű **Beállítási** elemből, amely a szolgáltatáskonfigurációs fájlban található.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.

## <a name="how-to-create-a-queue"></a>Útmutató: üzenetsor létrehozása
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

## <a name="how-to-add-a-message-to-a-queue"></a>Útmutató: üzenet hozzáadása egy várólistához
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

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: betekintés a következő üzenetbe
A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistából a **peekMessage**meghívásával.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: várólistán lévő üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Ha kiterjeszti a láthatósági időtúllépést, az üzenethez társított munkahelyi állapotot menti, és egy másik percet ad az ügyfélnek, hogy folytassa a munkát az üzeneten. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

A következő mintakód az üzenetek várólistáján keres, megkeresi az első üzenetet, amely megfelel a tartalomnak a "Hello, World" kifejezésnek, majd módosítja az üzenet tartalmát és kilép.

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

Másik lehetőségként a következő mintakód csak az első látható üzenetet frissíti a várólistán.

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

## <a name="how-to-get-the-queue-length"></a>Útmutató: a várólista hosszának beolvasása
Megbecsülheti egy üzenetsorban található üzenetek számát. A **downloadAttributes** metódus több aktuális értékhez kérdezi le a Queue szolgáltatást, többek között annak számát, hogy hány üzenet van egy várólistában. A darabszám csak hozzávetőleges, mert az üzenetek hozzáadhatók vagy eltávolíthatók, miután a Queue szolgáltatás válaszol a kérelemre. A **getApproximateMessageCount** metódus a **downloadAttributes**hívása által lekért utolsó értéket adja vissza a Queue szolgáltatás hívása nélkül.

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

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: a következő üzenet elküldése
A kód két lépésben elvégez egy üzenetsor-várólistán lévő üzenetet. A **retrieveMessage**meghívásakor a következő üzenet jelenik meg egy várólistában. A **retrieveMessage** által visszaadott üzenet láthatatlanná válik minden más, a várólistáról olvasó üzenetben. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez a **deleteMessage**is hívnia kell. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód a **deleteMessage** közvetlenül az üzenet feldolgozása után hívja meg.

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

## <a name="additional-options-for-dequeuing-messages"></a>További beállítások a dequeuing üzenetek számára
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket.

A következő kódrészlet a **retrieveMessages** metódus használatával 20 üzenetet kap egy hívásban. Ezután feldolgozza az egyes üzeneteket a **for** loop használatával. Azt is beállítja, hogy az egyes üzenetek esetében a láthatósági időkorlát 5 perc (300 másodperc) legyen. Az összes üzenetet egyszerre 5 percben indítja el, így ha a **retrieveMessages**hívása óta öt perc telt el, akkor a nem törölt üzenetek ismét láthatóvá válnak.

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

## <a name="how-to-list-the-queues"></a>Útmutató: a várólisták listázása
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

## <a name="how-to-delete-a-queue"></a>Útmutató: üzenetsor törlése
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

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

> [!NOTE]
> Egy SDK elérhető az Azure Storage-et Android-eszközökön használó fejlesztők számára. További információk: [Androidhoz készült Azure Storage SDK][Azure Storage SDK for Android].

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a várólista-tárolás alapjait, az alábbi hivatkozásokat követve megismerheti az összetettebb tárolási feladatokat.

* [Javához készült Azure Storage SDK][Azure Storage SDK for Java]
* [Azure Storage ügyféloldali SDK-referencia][Azure Storage Client SDK Reference]
* [Az Azure Storage-szolgáltatások REST API-ja][Azure Storage Services REST API]
* [Az Azure Storage csapat blogja][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage ügyféloldali SDK-referencia]: https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
