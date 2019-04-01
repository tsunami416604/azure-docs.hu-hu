---
title: A Queue storage, a Java használatával |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Queue szolgáltatás létrehozása és törlése, üzenetsorok, és helyezze, beolvasása, és törli az üzenetet. Java nyelven írt mintát.
services: storage
author: roygara
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 7c2fbc168c663cc4dc5981071d40540cfc4797ca
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648486"
---
# <a name="how-to-use-queue-storage-from-java"></a>How to use Queue Storage from Java (A Queue Storage használata Javával)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre az Azure Queue storage szolgáltatást használó általános forgatókönyvhöz. A kódminták Java nyelven íródtak, és a [Java-hoz készült Azure Storage SDK-t][Azure Storage SDK for Java] használják. Az ismertetett forgatókönyvek között megtalálható **beszúrása**, **Bepillantás**, **első**, és **törlése** várólista-üzenetek, valamint  **létrehozás** és **törlése** üzenetsorok. Üzenetsorok további információkért lásd: a [további lépések](#next-steps) szakaszban.

Megjegyzés: Egy SDK elérhető az Azure Storage-et Android-eszközökön használó fejlesztők számára. További információk: [Androidhoz készült Azure Storage SDK][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
Ebben az útmutatóban helyileg, vagy egy adott webes szerepkör vagy a feldolgozói szerepkör az Azure-ban futó Java-alkalmazás belül futó storage szolgáltatásokat fogja használni.

Ehhez szüksége lesz a Java fejlesztői készlet (JDK) telepítése, és az Azure storage-fiók létrehozása az Azure-előfizetésében. Amint még meg, fel kell annak ellenőrzése, hogy a fejlesztői rendszerhez megfelel-e a minimális követelményeit és függőségeit, amelyek szerepelnek a [Javához készült Azure Storage SDK] [ Azure Storage SDK for Java] tárházban a Githubon. Ha a rendszer megfelel ezeknek a követelményeknek, követheti a következő útmutatót: letöltése és telepítése a Javához készült Azure Storage-könyvtárak a rendszer az adott tárházba. Miután végrehajtotta ezeket a feladatokat, lesz egy ebben a cikkben szereplő példák használó Java-alkalmazás létrehozása.

## <a name="configure-your-application-to-access-queue-storage"></a>A queue storage eléréséhez az alkalmazás konfigurálása
Adja hozzá a következő importálási utasításokat a Java-fájlt, amelyre az Azure storage API-k várólisták eléréséhez használható elejéhez:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ha egy ügyfélalkalmazás fut, meg kell adnia a tárolási kapcsolati karakterlánc a következő formátumban a tárfiók neve és a tárfiók elsődleges hívóbetűjét szerepel a [az Azure Portal](https://portal.azure.com) a a *AccountName* és *AccountKey* értékeket. Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Egy alkalmazás fut, a Microsoft Azure-ban szerepkörökben, ez a karakterlánc is tárolja a konfigurációs fájlban, *ServiceConfiguration.cscfg*, és hívásával érhetők el a  **RoleEnvironment.getConfigurationSettings** metódust. A következő példa bemutatja, hogyan kérhető le egy kapcsolati sztring egy *StorageConnectionString* nevű **Beállítási** elemből, amely a szolgáltatáskonfigurációs fájlban található.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.

## <a name="how-to-create-a-queue"></a>Útmutató: Üzenetsor létrehozása
A **CloudQueueClient** objektum lehetővé teszi a várólisták hivatkozási objektumok beolvasása. Az alábbi kód létrehoz egy **CloudQueueClient** objektum. (Megjegyzés: Hozzon létre további módon **CloudStorageAccount** objektumok; további információkért lásd: **CloudStorageAccount** a a [Azure Storage ügyféloldali SDK-referencia].)

Használja a **CloudQueueClient** beolvasása egy hivatkozást a használni kívánt várólista-objektum. A várólista hozhat létre, ha még nem létezik.

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

## <a name="how-to-add-a-message-to-a-queue"></a>Útmutató: Adjon meg egy üzenetet egy üzenetsorba
Ha üzenetet szeretne beszúrni egy létező üzenetsorba, először hozzon létre egy **CloudQueueMessage** elemet. Ezután hívja a **addMessage** metódust. A **CloudQueueMessage** egy karakterláncból (UTF-8 formátumban) vagy Bájttömbbel hozható létre. A következő kódot, amely létrehoz egy üzenetsort (Ha még nem létezik), és szúrja be a "Hello, World" üzenetet.

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

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: Betekintés a következő üzenetbe
Anélkül, hogy eltávolítaná az üzenetsorból meghívásával is bepillanthat egy üzenetsorban található üzenet **peekMessage**.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: Üzenetsorban található üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

A következő kód minta keresések végig az üzenetsoron, az üzenetek, megkeresi az első üzenet, amely a "Hello, World" megegyezik a tartalomhoz, majd módosítja a tartalom üzenetet, és kilép.

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

A következő példakód azt is megteheti, frissíti a csak az első látható üzenet az üzenetsorban.

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

## <a name="how-to-get-the-queue-length"></a>Útmutató: Az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **downloadAttributes** módszert kéri az üzenetsor-szolgáltatás több aktuális értékeit, többek között hány üzenetek vannak egy üzenetsorban lévő számát. A számláló értéke csak hozzávetőleges, mert üzeneteket is lehet hozzáadni vagy eltávolítani után a Queue szolgáltatás válaszol a kérésre. A **getApproximateMessageCount** metódus hívása által lekért legutóbbi értéket ad vissza **downloadAttributes**, a Queue szolgáltatás hívása nélkül.

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

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: A következő üzenet eltávolítása a sorból
A kód két lépésben egy üzenetsorból üzenetet dequeues. Meghívásakor **retrieveMessage**, a következő üzenetet kap egy üzenetsorban. Lekért üzenet **retrieveMessage** válik az adott üzenetsorban üzeneteket olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Befejeződik, az üzenet eltávolítása az üzenetsorból, meg kell is meghívhat **deleteMessage**. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód meghívja **deleteMessage** jobb gombbal az üzenet feldolgozása után.

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

## <a name="additional-options-for-dequeuing-messages"></a>További beállítások üzenetmozgatót üzenetek
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket.

Az alábbi példakód a **retrieveMessages** metódus lekéréséhez 20 üzenetet egyetlen hívásával. Ezután minden üzenetet használatával feldolgozza a **a** ciklus. Állítja be az láthatatlansági időkorlátot minden üzenethez öt percre (300 másodperc). Vegye figyelembe, hogy az öt perc alatt elindítja az összes üzenetet egy időben, ezért ha öt perc alatt megfeleltek hívása **retrieveMessages**, nem törölt üzenetek újra láthatóvá válnak.

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

## <a name="how-to-list-the-queues"></a>Útmutató: Az üzenetsorok listázása
Az aktuális sor egy listája beszerzéséhez hívja meg a **CloudQueueClient.listQueues()** metódussal, amely egy gyűjteményét adja vissza **CloudQueue** objektumokat.

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

## <a name="how-to-delete-a-queue"></a>Útmutató: Üzenetsor törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja a **deleteIfExists** metódust a **CloudQueue** objektum.

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
Most, hogy megismerte a queue storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok.

* [A Javához készült Azure Storage SDK][Azure Storage SDK for Java]
* [Azure Storage ügyféloldali SDK-referencia][Azure Storage ügyféloldali SDK-referencia]
* [Az Azure Storage szolgáltatások REST API][Azure Storage Services REST API]
* [Az Azure Storage csapat blogja][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage ügyféloldali SDK-referencia]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
