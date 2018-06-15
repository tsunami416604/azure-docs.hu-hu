---
title: A Java a Queue storage használata |} Microsoft Docs
description: Útmutató az Azure Queue szolgáltatás segítségével hozza létre, és törli az üzenetsorok, és helyezze, get, és törli az üzenetet. A Java nyelven írt minták.
services: storage
documentationcenter: java
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 68cecc8e-38c9-4a24-99e8-cb722bc63cf9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.openlocfilehash: cd79a21fedca1c21c2e4a65394d424f1590f32c3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30319481"
---
# <a name="how-to-use-queue-storage-from-java"></a>How to use Queue Storage from Java (A Queue Storage használata Javával)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a szolgáltatást az Azure Queue storage szolgáltatást használó általános forgatókönyvhöz. A mintákat a Java és -felhasználási nyelven íródtak a [Azure Storage SDK for Java][Azure Storage SDK for Java]. Az ismertetett forgatókönyvek **beszúrása**, **megtekintésekor**, **első**, és **törlése** üzenetek várólistára, valamint **létrehozása** és **törlése** várólisták. A várólisták további információkért lásd: a [további lépések](#Next-Steps) szakasz.

Megjegyzés: Az SDK nem elérhető a fejlesztők számára, akik Azure Storage Android-eszközökön. További információkért lásd: a [Azure Storage SDK for Android][Azure Storage SDK for Android].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
Ez az útmutató a tárolási szolgáltatásokkal, amelyek helyben, vagy a webes szerepkör vagy a feldolgozói szerepkör az Azure-ban futó belül Java-alkalmazások futtatása fogja használni.

Ehhez szüksége lesz a Java fejlesztői készlet (JDK) telepítése, és hozzon létre egy Azure storage-fiókot az Azure-előfizetéshez. Ha ezzel végzett, akkor győződjön meg arról, hogy a fejlesztési rendszer megfelel-e a minimális követelményeit és függőségeit, amelyek szerepelnek a [Azure Storage SDK for Java] [ Azure Storage SDK for Java] GitHub tárházából. Ha a rendszer megfelel ezeknek a követelményeknek, akkor is kövesse letöltése és telepítése az Azure Storage szalagtárak Java ebből a rendszeren. Ezek a feladatok befejezése után lesz ebben a cikkben a példák használó Java-alkalmazás létrehozása.

## <a name="configure-your-application-to-access-queue-storage"></a>Állítsa be az alkalmazását, a queue storage eléréséhez
Vegye fel a következő importálási utasításokat a felső részén a Java-fájlt, amelyre az Azure storage API-k várólisták eléréséhez használható:

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
Egy Azure storage-ügyfél egy tárolási kapcsolati karakterlánc végpontok és adatok szolgáltatások eléréséhez szükséges hitelesítő adatok tárolására használ. Ha egy ügyfél-alkalmazás fut, meg kell adnia a tárolási kapcsolati karakterlánc a következő formátumban a tárfiók nevével, és a tárfiók elsődleges elérési kulcs szerepel az [Azure Portal](https://portal.azure.com) a a *AccountName* és *AccountKey* értékek. Ez a példa bemutatja, hogyan deklarálhatnak ahhoz, hogy a kapcsolati karakterlánc statikus mezőben:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Egy alkalmazás fut, a Microsoft Azure-ban a szerepkörön belüli, az ezt a karakterláncot tárolhatja a konfigurációs fájlban, *ServiceConfiguration.cscfg*, és hívja érhető el a **RoleEnvironment.getConfigurationSettings** metódust. Íme egy példa a kapcsolati karakterlánc beolvasása egy **beállítás** nevű elem *StorageConnectionString* a konfigurációs fájlban:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

A következő minták azt feltételezik, hogy használt két módszer közül egyik beolvasni a tárolási kapcsolati karakterlánc.

## <a name="how-to-create-a-queue"></a>Útmutató: a várólista létrehozása
A **CloudQueueClient** objektum lehetővé teszi a várólisták hivatkozási objektumok beolvasása. Az alábbi kód létrehoz egy **CloudQueueClient** objektum. (Megjegyzés: további módon létrehozásához **CloudStorageAccount** objektumok; további információkért lásd: **CloudStorageAccount** a a [Azure Storage ügyfél SDK-dokumentáció].)

Használja a **CloudQueueClient** hivatkozás a használni kívánt várólista-objektum. A várólista hozhat létre, ha még nem létezik.

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

## <a name="how-to-add-a-message-to-a-queue"></a>Útmutató: üzenet várólista hozzáadása
Ha üzenetet szeretne beszúrni egy létező üzenetsorba, először hozzon létre egy **CloudQueueMessage** elemet. Ezután hívja a **addMessage** metódust. A **CloudQueueMessage** is létrehozható egy karakterláncból (UTF-8 formátumban), illetve egy bájttömböt. Itt található kód létrehoz egy üzenetsort (Ha még nem létezik), és szúrja be a "Hello, World" üzenet.

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

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: a következő üzenet megtekintése
Is bepillanthat, hogy egy sor elején található üzenetbe anélkül, hogy eltávolítaná az üzenetsorból meghívásával **peekMessage**.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: az üzenetsorban található üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

A következő kód a minta keresésre a várólistában lévő üzenetek, megkeresi az első üzenet, amely a "Hello, World" megegyezik a tartalomhoz, majd módosítja a tartalom üzenet, és kilép.

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

A következő példakód azt is megteheti, csak az első látható üzenet a várólistában frissíti.

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

## <a name="how-to-get-the-queue-length"></a>Útmutató: az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **downloadAttributes** módszert kéri a Queue szolgáltatás több aktuális értékhez, beleértve az üzenetek számának vannak a várólistán lévő számát. A szám nem csak hozzávetőleges, mert az üzenetek hozzáadására vagy törlődik, miután a Queue szolgáltatás válaszol a kérésre. A **getApproximateMessageCount** metódus hívása által lekért legutóbbi értéket ad vissza **downloadAttributes**, a Queue szolgáltatás hívása nélkül.

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

## <a name="how-to-dequeue-the-next-message"></a>Útmutató: a következő üzenet created
A kód dequeues egy üzenetet az üzenetsorból két lépésben. A hívás esetén **retrieveMessage**, a következő üzenetet kap a sorhoz. Az üzenet **retrieveMessage** ebből a várólistából üzeneteket olvasó többi kód láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Szeretné távolítani az üzenetet az üzenetsorból, meg kell is hívni **deleteMessage**. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód hívások **deleteMessage** jobb gombbal az üzenet feldolgozása után.

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

Az alábbi példakód a **retrieveMessages** metódus használatával kérje le a 20 üzenetet egy hívásban. Ezután minden üzenetet használatával feldolgozza a **a** hurok. Azt is meghatározza a láthatatlansági időkorlátot minden üzenethez öt percre (300 másodperc). Vegye figyelembe, hogy az öt perc elindítja az összes üzenet egyszerre, így ha öt perc telt hívása **retrieveMessages**, nem törölt üzenetek újra láthatóvá válnak.

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

## <a name="how-to-list-the-queues"></a>Útmutató: a várólisták felsorolása
Az aktuális várólisták listájának beszerzése, hívja meg a **CloudQueueClient.listQueues()** metódus, amely visszaadható gyűjteménye **CloudQueue** objektumok.

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

## <a name="how-to-delete-a-queue"></a>Útmutató: a várólista törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja meg a **deleteIfExists** metódust a **CloudQueue** objektum.

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
Most, hogy megismerte a queue storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről.

* [Az Azure Storage Java SDK][Azure Storage SDK for Java]
* [Az Azure Storage ügyfél SDK-dokumentáció][Azure Storage ügyfél SDK-dokumentáció]
* [Az Azure Storage szolgáltatások REST API-ja][Azure Storage Services REST API]
* [Az Azure Storage csapat blogja][Azure Storage Team Blog]

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage ügyfél SDK-dokumentáció]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
