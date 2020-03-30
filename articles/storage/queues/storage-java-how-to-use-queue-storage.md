---
title: A Java várólistára helyezett tárhelyének használata – Azure Storage
description: Ismerje meg, hogyan hozhat létre és törölhet várólistákat a Várólista-tároló használatával, és hogyan szúrhat be, kaphat be és törölhet üzeneteket az Azure Storage java-beli ügyfélkódtárjával.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 7658b8541e7a79a5e547a6649b35681446e34b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067133"
---
# <a name="how-to-use-queue-storage-from-java"></a>How to use Queue Storage from Java (A Queue Storage használata Javával)

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

Ez az útmutató bemutatja, hogyan hajthatja végre a gyakori forgatókönyveket az Azure Queue storage szolgáltatás használatával. A kódminták Java nyelven íródtak, és a [Java-hoz készült Azure Storage SDK-t][Azure Storage SDK for Java] használják. A tárgyalt forgatókönyvek közé tartozik a várólista-üzenetek **beszúrása,** **bepillantása,** **bepillantása**és **törlése,** valamint várólisták **létrehozása** és **törlése.** A várólistákról a [Következő lépések](#next-steps) című szakaszban talál további információt.

> [!NOTE]
> Egy SDK elérhető az Azure Storage-et Android-eszközökön használó fejlesztők számára. További információk: [Androidhoz készült Azure Storage SDK][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása

Ebben az útmutatóban olyan tárolási funkciókat fog használni, amelyek helyileg futtathatók egy Java-alkalmazáson belül, vagy egy webalkalmazáson belül az Azure-ban futó kódban.

Ehhez telepítenie kell a Java Development Kit (JDK) készletet, és létre kell hoznia egy Azure-tárfiókot az Azure-előfizetésében. Miután ezt megtette, ellenőriznie kell, hogy a fejlesztési rendszer megfelel-e a githubon az [Azure Storage SDK][Azure Storage SDK for Java] Java-tárházban felsorolt minimális követelményeknek és függőségeknek. Ha a rendszer megfelel ezeknek a követelményeknek, kövesse az Azure Storage Libraries for Java a rendszer a rendszer, hogy a tárházból letöltésére és telepítésére vonatkozó utasításokat. Miután elvégezte ezeket a feladatokat, létrehozhat egy Java-alkalmazást, amely a jelen cikkben szereplő példákat használja.

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása a várólista-tároló eléréséhez

Adja hozzá a következő importálási nyilatkozatokat a Java-fájl tetejéhez, ahol az Azure storage API-kat szeretné használni a várólisták eléréséhez:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure storage-kapcsolati karakterlánc beállítása

Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ügyfélalkalmazásban történő futtatáskor a tárolási kapcsolati sztringet a következő formátumban kell megadnia: Használja a Storage-fiók nevét és az [Azure Portalon](https://portal.azure.com) megtalálható elsődleges hozzáférési kulcsát az *AccountName* és *AccountKey* értékeként. Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

A Microsoft Azure szerepkörén belül futó alkalmazásokban ez a karakterlánc a *ServiceConfiguration.cscfg*szolgáltatáskonfigurációs fájlban tárolható, és a **RoleEnvironment.getConfigurationSettings** metódus hívásával érhető el. A következő példa bemutatja, hogyan kérhető le egy kapcsolati sztring egy *StorageConnectionString* nevű **Beállítási** elemből, amely a szolgáltatáskonfigurációs fájlban található.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.

## <a name="how-to-create-a-queue"></a>Útmutató: Várólista létrehozása
A **CloudQueueClient** objektum lehetővé teszi a várólisták referenciaobjektumainak lefoglalását. A következő kód létrehoz egy **CloudQueueClient** objektumot. (Megjegyzés: A **CloudStorageAccount-objektumok** létrehozásának további módjai is rendelkezésre állnak; további információkért lásd: **CloudStorageAccount** az [Azure Storage Ügyfél SDK-hivatkozásban.)]

A **CloudQueueClient** objektum segítségével hivatkozást kaphat a használni kívánt várólistára. Létrehozhatja a várólistát, ha nem létezik.

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

## <a name="how-to-add-a-message-to-a-queue"></a>Útmutató: Üzenet hozzáadása várólistához
Ha üzenetet szeretne beszúrni egy létező üzenetsorba, először hozzon létre egy **CloudQueueMessage** elemet. Ezután hívja meg az **addMessage** metódust. A **CloudQueueMessage** egy sztringből (UTF-8 formátumban) vagy egy bájttömbből hozható létre. Itt van egy kód, amely létrehoz egy várólistát (ha nem létezik), és beszúrja a "Hello, World" üzenetet.

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

## <a name="how-to-peek-at-the-next-message"></a>Hogyan: Peek a következő üzenet
A várólista elején lévő üzenetbe anélkül tekinthet meg, hogy eltávolítaná a várólistából a **peekMessage**hívásával.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: A várólistán lévő üzenetek tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. A láthatósági időtúltávolság meghosszabbítása menti az üzenethez társított munkaállapotot, és az ügyfélnek még egy percet ad az üzeneten való munka folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

A következő kódminta az üzenetek várólistáján keresztül keres, megkeresi az első üzenetet, amely megfelel a "Hello, World" üzenetnek a tartalomhoz, majd módosítja az üzenet tartalmát és kilép.

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

Másik lehetőségként a következő kódminta csak az első látható üzenetet frissíti a várólistán.

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

## <a name="how-to-get-the-queue-length"></a>Útmutató: A várólista hosszának beszereznie
Megbecsülheti egy üzenetsorban található üzenetek számát. A **downloadAttributes** metódus több aktuális értéket kér meg a Várólista szolgáltatástól, beleértve annak számát is, hogy hány üzenet van egy várólistában. A számláló csak hozzávetőleges, mert az üzenetek hozzáadhatók vagy eltávolíthatók, miután a Várólista szolgáltatás válaszol a kérésre. A **getApproximateMessageCount** metódus a **downloadAttributes**hívás által beolvasott utolsó értéket adja vissza a Várólista szolgáltatás hívása nélkül.

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

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: A következő üzenet várólistájának törlése
A kód két lépésben távolodik el egy üzenettől a várólistából. A **retrieveMessage**hívásakor a következő üzenet jelenik meg a várólistában. A **retrieveMessage** üzenetből visszaadott üzenet láthatatlanná válik a várólistából érkező üzeneteket olvasó bármely más kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívnia a **deleteMessage parancsot**is. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód kéri **deleteMessage** közvetlenül az üzenet feldolgozása után.

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

## <a name="additional-options-for-dequeuing-messages"></a>Az üzenetek sorba oltó dinamtálásának további lehetőségei
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket.

A következő kód példa a **retrieveMessages** metódust használja 20 üzenet lekéréséhez egy hívásban. Ezután feldolgozza az egyes üzeneteket egy **for** ciklus használatával. Azt is beállítja a láthatatlanság időtúltávolság öt perc (300 másodperc) minden üzenet. Az összes üzenet öt perce egyszerre kezdődik, így ha öt perc telt el az **Üzenetek letöltésére**irányuló hívás óta, a nem törölt üzenetek ismét láthatóvá válnak.

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

## <a name="how-to-list-the-queues"></a>Útmutató: A várólisták listázása
Az aktuális várólisták listájának beszerzéséhez hívja meg a **CloudQueueClient.listQueues()** metódust, amely a **CloudQueue** objektumok gyűjteményét adja vissza.

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

## <a name="how-to-delete-a-queue"></a>Útmutató: Várólista törlése
Egy várólista és a benne lévő összes üzenet törléséhez hívja meg a **deleteIfExists** metódust a **CloudQueue** objektumon.

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

## <a name="next-steps"></a>További lépések
Most, hogy megtanulta a várólista-tárolás alapjait, kövesse ezeket a hivatkozásokat az összetettebb tárolási feladatok megismeréséhez.

* [Azure Storage SDK Java-hoz][Azure Storage SDK for Java]
* [Azure Storage ügyféloldali SDK-referencia][Azure Storage Client SDK Reference]
* [Az Azure Storage-szolgáltatások REST API-ja][Azure Storage Services REST API]
* [Az Azure Storage-csapat blogja][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage ügyféloldali SDK-referencia]: https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
