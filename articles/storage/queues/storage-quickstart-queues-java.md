---
title: 'Gyors útmutató: Azure üzenetsor Storage Library V12 – Java'
description: Megtudhatja, hogyan hozhat létre üzenetsor létrehozásához és üzeneteket a várólistához az Azure-üzenetsor Java V12-es kódtár használatával. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket a várólistából. Azt is megtudhatja, hogyan törölhet egy várólistát.
author: mhopkins-msft
ms.custom: devx-track-java
ms.author: mhopkins
ms.date: 12/4/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 753c245631d2fbdc8122f955c1db6562a1554031
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319371"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Gyors útmutató: Azure üzenetsor Storage ügyféloldali kódtára a Javához

Ismerkedjen meg az Azure üzenetsor Storage ügyféloldali kódtár 12-es verziójával a Javához. Az Azure üzenetsor-tároló egy olyan szolgáltatás, amely nagy mennyiségű üzenetet tárol a későbbi lekérésekhez és feldolgozáshoz. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja a programkódot az alapszintű feladatokhoz.

Használja az Azure üzenetsor Storage ügyféloldali kódtárat a Javához a következőhöz:

* Üzenetsor létrehozása
* Üzenetek hozzáadása egy várólistához
* Üzenetek betekintése egy várólistába
* Üzenetsor frissítése
* Üzenetek fogadása és törlése egy várólistából
* Üzenetsor törlése

További források:

* [API-referenciadokumentáció](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html)
* [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
* [Csomag (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
* [Példák](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

* A [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) 8-as vagy újabb verziója
* [Apache Maven](https://maven.apache.org/download.cgi)
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Beállítás

Ebből a szakaszból megtudhatja, hogyan készít elő egy projektet az Azure üzenetsor-tárolóval való együttműködéshez a Javához.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *Queues-Gyorsindítás-V12*nevű Java-alkalmazást.

1. A konzol ablakban (például a cmd, a PowerShell vagy a bash) a Maven használatával hozzon létre egy új Console-alkalmazást a *Queues-Gyorsindítás-V12*néven. A "Hello World!" létrehozásához írja be a következő **MVN** -parancsot: Java-projekt.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
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
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Váltson az újonnan létrehozott *várólistákra – rövid útmutató – V12* könyvtár.

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a függőségek csoportjához a következő függőségi elemet.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Navigáljon a */src/Main/Java/com/Queues/Quickstart* könyvtárához
1. Nyissa meg az *app. Java* fájlt a szerkesztőben
1. Az `System.out.println("Hello world!");` utasítás törlése
1. `import`Irányelvek hozzáadása

A kód a következő:

```java
package com.queues.quickstart;

/**
 * Azure queue storage v12 SDK quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra. A várólista-tárolás háromféle típusú erőforrást kínál:

* A Storage-fiók
* A Storage-fiókban lévő üzenetsor
* Üzenetek a várólistán belül

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Üzenetsor-tárolási architektúra ábrája](./media/storage-queues-introduction/queue1.png)

A következő Java-osztályok használhatók az alábbi erőforrásokkal való kommunikációhoz:

* [QueueClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClientBuilder.html): a `QueueClientBuilder` osztály egy objektumot konfigurál és hoz létre `QueueClient` .
* [QueueServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueServiceClient.html): a `QueueServiceClient` lehetővé teszi a Storage-fiók összes várólistájának kezelését.
* [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html): a `QueueClient` osztály segítségével kezelheti és kezelheti az egyes várólistákat és azok üzeneteit.
* [QueueMessageItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/QueueMessageItem.html): az `QueueMessageItem` osztály a [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-) egy várólistán való meghívásakor visszaadott egyedi objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő műveleteket az Azure üzenetsor-tárolás ügyféloldali kódtára Javával:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Üzenetsor létrehozása](#create-a-queue)
* [Üzenetek hozzáadása egy várólistához](#add-messages-to-a-queue)
* [Üzenetek betekintése egy várólistába](#peek-at-messages-in-a-queue)
* [Üzenetsor frissítése](#update-a-message-in-a-queue)
* [Üzenetek fogadása és törlése egy várólistából](#receive-and-delete-messages-from-a-queue)
* [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage-fiókhoz tartozó kapcsolatok karakterláncot kérdezi le. A rendszer tárolja a hálózati karakterláncot a [tárolási kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóban.

Adja hozzá ezt a kódot a `main` metódushoz:

```java
System.out.println("Azure Queues storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Üzenetsor létrehozása

Döntse el az új üzenetsor nevét. Az alábbi kód egy GUID értéket fűz hozzá a várólista neveként, hogy az egyedi legyen.

> [!IMPORTANT]
> A várólisták nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3 – 63 karakter hosszúnak kell lennie. További információ a várólisták elnevezéséről: [várólisták és metaadatok elnevezése](/rest/api/storageservices/naming-queues-and-metadata).


Hozza létre a [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) osztály egy példányát. Ezután hívja meg a [create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#create--) metódust az üzenetsor létrehozásához a Storage-fiókban.

Adja hozzá ezt a kódot a metódus végéhez `main` :

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása egy várólistához

A következő kódrészlet üzeneteket hoz létre a várólistához a [üzenetküldés](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#sendMessage-java.lang.String-) metódus meghívásával. Egy hívásból visszaadott [SendMessageResult](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/SendMessageResult.html) is ment `sendMessage` . Az eredmény az üzenet későbbi, a programban való frissítésére szolgál.

Adja hozzá ezt a kódot a metódus végéhez `main` :

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Üzenetek betekintése egy várólistába

A [peekMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#peekMessages-java.lang.Integer-java.time.Duration-com.azure.core.util.Context-) metódus meghívásával megtekintheti az üzenetsor üzeneteit. A `peelkMessages` metódus egy vagy több üzenetet kérdez le a várólista elejéről, de nem módosítja az üzenet láthatóságát.

Adja hozzá ezt a kódot a metódus végéhez `main` :

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Üzenetsor frissítése

Egy üzenet tartalmának frissítéséhez hívja meg a [updateMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#updateMessage-java.lang.String-java.lang.String-java.lang.String-java.time.Duration-) metódust. A `updateMessage` metódus módosíthatja az üzenet láthatósági időtúllépését és tartalmát. Az üzenet tartalmának UTF-8 kódolású karakterláncnak kell lennie, amely akár 64 KB méretű is lehet. Az üzenethez tartozó új tartalom mellett adja át az üzenet AZONOSÍTÓját és a pop-nyugtát a `SendMessageResult` kódban korábban mentett fájl használatával. Az üzenet azonosítója és a pop-visszaigazolás azonosítja a frissítendő üzenetet.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Üzenetek fogadása és törlése egy várólistából

A korábban hozzáadott üzenetek letöltése a [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-java.time.Duration-java.time.Duration-com.azure.core.util.Context-) metódus meghívásával. A példában a kód a fogadás és a feldolgozás után is törli az üzeneteket a várólistából. Ebben az esetben a feldolgozás csak az üzenet megjelenítését jeleníti meg a konzolon.

Az alkalmazás a felhasználói bevitel szüneteltetését hívja `System.console().readLine();` meg az üzenetek fogadása és törlése előtt. A törlés előtt ellenőrizze, hogy az erőforrások megfelelően lettek-e létrehozva a [Azure Portal](https://portal.azure.com) . A nem explicit módon törölt üzenetek később ismét láthatóvá válnak a várólistában, hogy egy másik lehetőség is feldolgozza őket.

Adja hozzá ezt a kódot a metódus végéhez `main` :

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Üzenetsor törlése

A következő kód megtisztítja az alkalmazás által létrehozott erőforrásokat az üzenetsor törlésével a [delete](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#delete--) metódus használatával.

Adja hozzá ezt a kódot a metódus végéhez `main` :

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre és tesz hozzá egy Azure-várólistához. A kód felsorolja a várólistán lévő üzeneteket, majd lekéri és törli őket, mielőtt véglegesen törölné a várólistát.

A konzol ablakban navigáljon az alkalmazás könyvtárába, majd hozza létre és futtassa az alkalmazást.

```console
mvn compile
```

Ezután hozza létre a csomagot.

```console
mvn package
```

Futtassa az alábbi `mvn` parancsot az alkalmazás végrehajtásához.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Queues storage v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

Ha az alkalmazás szünetelteti az üzenetek fogadását, akkor a [Azure Portalban](https://portal.azure.com)keresse meg a Storage-fiókját. Ellenőrizze, hogy az üzenetek szerepelnek-e a várólistán.

Nyomja le az **ENTER** billentyűt az üzenetek fogadásához és törléséhez. Ha a rendszer kéri, nyomja meg ismét az **ENTER** billentyűt a várólista törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre várólistát, és hogyan adhat hozzá üzeneteket a Java-kóddal. Ezután megtanulta az üzenetek betekintését, beolvasását és törlését. Végezetül megtanulta, hogyan törölhet egy üzenetsor-várólistát.

Oktatóanyagok, minták, gyors indítás és egyéb dokumentáció:

> [!div class="nextstepaction"]
> [Azure Java-felhőfejlesztőknek](https://docs.microsoft.com/azure/developer/java/)

* További Azure üzenetsor-tárolási minta alkalmazások megjelenítéséhez folytassa az [Azure üzenetsor Storage SDK V12 Java ügyféloldali függvénytár-mintákkal](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue).
