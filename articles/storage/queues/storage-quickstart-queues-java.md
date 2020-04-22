---
title: 'Rövid útmutató: Az Azure Queue storage tára v12 – Java'
description: Ismerje meg, hogyan használhatja az Azure Queue Java v12-kódtár segítségével egy várólista létrehozásához és üzenetek hozzáadásához a várólistához. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket a várólistából. Azt is megtudhatja, hogyan törölhet idát várólistát.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/4/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 013281db2821c99f6942edd1322f4978e100c144
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729848"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Rövid útmutató: Azure Queue storage ügyfélkódtár v12 Java-hoz

Ismerkedés az Azure Queue storage ügyfélkódtár 12-es Java-verziójával. Az Azure Queue storage egy olyan szolgáltatás, amely nagy számú üzenetet tárol későbbi lekéréshez és feldolgozáshoz. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját.

Használja az Azure Queue storage ügyfélkódtár v12 Java a következőkhöz:

* Üzenetsor létrehozása
* Üzenetek hozzáadása várólistához
* Betekintés a várólistában lévő üzenetekbe
* Üzenet frissítése a várólistában
* Üzenetek fogadása és törlése várólistából
* Üzenetsor törlése

[API-referenciadokumentáció](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/index.html) | [könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) | [csomag (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue) | [mintái](https://docs.microsoft.com/azure/storage/common/storage-samples-java?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

* [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable) 8-as vagy újabb verzió
* [Apache Maven](https://maven.apache.org/download.cgi)
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure storage-fiók – [tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti a projekt előkészítése az Azure Queue storage ügyfélkódtár v12 Java-hoz való használatra.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy Java alkalmazás nevű *várólisták-quickstart-v12*.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) a Maven használatával hozzon létre egy új konzolalkalmazást a *gyorsindítás-v12*névvel. "Hello world!" parancs létrehozásához írja be a következő **mvn** parancsot. Java projekt.

   ```console
   mvn archetype:generate -DgroupId=com.queues.quickstart \
                          -DartifactId=queues-quickstart-v12 \
                          -DarchetypeArtifactId=maven-archetype-quickstart \
                          -DarchetypeVersion=1.4 \
                          -DinteractiveMode=false
   ```

1. A projekt generálásának kimenete valahogy így fog kinézni:

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

1. Váltson át az újonnan létrehozott *várólisták-quickstart-v12* könyvtárra.

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a *pom.xml* fájlt a szövegszerkesztőben. Adja hozzá a következő függőségi elemet a függőségek csoportjához.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazáskeretrendszer beállítása

A projekt könyvtárából:

1. Keresse meg a */src/main/java/com/queues/quickstart* könyvtárat
1. Az *App.java* fájl megnyitása a szerkesztőben
1. Az `System.out.println("Hello world!");` utasítás törlése
1. Direktívák hozzáadása `import`

Itt a kód:

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

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. A várólista-üzenet mérete legfeljebb 64 KB lehet. Egy várólista több millió üzenetet tartalmazhat, a tárfiók teljes kapacitáskorlátjáig. A várólistákat gyakran használják az aszinkron feldolgozáshoz használt munka hátralékának létrehozására. A várólista-tárolás háromféle erőforrást kínál:

* A tárfiók
* Várólista a tárfiókban
* Üzenetek a várólistán belül

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Várólista tárolási architektúrájának diagramja](./media/storage-queues-introduction/queue1.png)

Az alábbi Java-osztályok segítségével kezelheti ezeket az erőforrásokat:

* [QueueClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClientBuilder.html): `QueueClientBuilder` Az osztály konfigurálja és `QueueClient` példányosodik egy objektumot.
* [QueueServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueServiceClient.html): `QueueServiceClient` A lehetővé teszi a tárfiók összes várólistájának kezelését.
* [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html): `QueueClient` Az osztály lehetővé teszi egy adott várólista és üzenetének kezelését és kezelését.
* [QueueMessageItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/QueueMessageItem.html): `QueueMessageItem` Az osztály a [várólistán lévő receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-) hívásakor visszaadott egyes objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek bemutatják, hogyan teheti meg a következő műveleteket az Azure Queue storage java-beli ügyfélkódtárjával:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Üzenetsor létrehozása](#create-a-queue)
* [Üzenetek hozzáadása várólistához](#add-messages-to-a-queue)
* [Betekintés a várólistában lévő üzenetekbe](#peek-at-messages -in-a-queue)
* [Üzenet frissítése a várólistában](#update-a-message-in-a-queue)
* [Üzenetek fogadása és törlése várólistából](#receive-and-delete-messages-from-a-queue)
* [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati karakterláncát. A kapcsolati karakterlánc a [tárolási kapcsolati karakterlánc konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változót tárolja.

Adja hozzá ezt `main` a kódot a metódushoz:

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

Döntse el az új várólista nevét. Az alábbi kód hozzáfűz egy GUID értéket a várólista nevéhez, hogy megbizonyosodjon arról, hogy az egyedi.

> [!IMPORTANT]
> A várólistanevek csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődniük. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3 és 63 karakter közötti nek is kell lennie. A várólisták elnevezéséről további információt az [Elnevezési várólisták és a metaadatok](/rest/api/storageservices/naming-queues-and-metadata)című témakörben talál.


Hozzon létre egy példányt a [QueueClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html) osztályból. Ezután hívja meg a [létrehozási](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#create--) módszert a várólista létrehozásához a tárfiókban.

Adja hozzá ezt a `main` kódot a módszer végéhez:

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

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása várólistához

A következő kódrészlet a [sendMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#sendMessage-java.lang.String-) metódus hívásával üzeneteket ad a várólistához. Egy `sendMessage` hívásból visszaadott [SendMessageResult-et](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/models/SendMessageResult.html) is ment. Az eredmény az üzenet későbbi frissítésére szolgál a program későbbi részében.

Adja hozzá ezt a `main` kódot a módszer végéhez:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Betekintés a várólistában lévő üzenetekbe

A [peekMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#peekMessages-java.lang.Integer-java.time.Duration-com.azure.core.util.Context-) metódus meghívásával tekintsünk be a várólistában lévő üzenetekbe. A `peelkMessages` metódus egy vagy több üzenetet olvas le a várólista elejéről, de nem változtatja meg az üzenet láthatóságát.

Adja hozzá ezt a `main` kódot a módszer végéhez:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Üzenet frissítése a várólistában

Az [updateMessage](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#updateMessage-java.lang.String-java.lang.String-java.lang.String-java.time.Duration-) metódus felhívásával frissítheti az üzenet tartalmát. A `updateMessage` módszer módosíthatja az üzenetek láthatósági időtúlságát és tartalmát. Az üzenet tartalmának UTF-8 kódolású karakterláncnak kell lennie, amely legfeljebb 64 KB méretű. Az üzenet új tartalmával együtt adja át az üzenetazonosítót `SendMessageResult` és a popnyugta-fogadást a kód korábbi menteni kívánt használatával. Az üzenetazonosító és az előugró nyugta azonosítja a frissítandó üzenetet.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(), 
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Üzenetek fogadása és törlése várólistából

Töltse le a korábban hozzáadott üzeneteket a [receiveMessages](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#receiveMessages-java.lang.Integer-java.time.Duration-java.time.Duration-com.azure.core.util.Context-) metódus felhívásával. A példakód a fogadás és feldolgozás után is törli az üzeneteket a várólistából. Ebben az esetben a feldolgozás csak az üzenet megjelenítése a konzolon.

Az alkalmazás szünetel a `System.console().readLine();` felhasználói bevitel hez, ha felhívja, mielőtt fogadja és törli az üzeneteket. Ellenőrizze az [Azure Portalon,](https://portal.azure.com) hogy az erőforrások megfelelően jöttek-e létre, mielőtt törölné őket. A kifejezetten nem törölt üzenetek végül ismét láthatóvá válnak a várólistában, hogy újabb esélyt kaphassanak azok feldolgozására.

Adja hozzá ezt a `main` kódot a módszer végéhez:

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

A következő kód törli az okat az erőforrásokat, amelyeket az alkalmazás a várólista [törlési](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-queue/12.0.0/com/azure/storage/queue/QueueClient.html#delete--) módszerrel történő törlésével hozott létre.

Adja hozzá ezt a `main` kódot a módszer végéhez:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre, és három üzenetet ad hozzá egy Azure-várólistához. A kód felsorolja az üzeneteket a várólistában, majd beolvassa és törli őket, mielőtt véglegesen törli a várólistát.

A konzolablakban keresse meg az alkalmazáskönyvtárat, majd építse fel és futtassa az alkalmazást.

```console
mvn compile
```

Ezután készítse el a csomagot.

```console
mvn package
```

Az alkalmazás `mvn` végrehajtásához futtassa a következő parancsot.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Az alkalmazás kimenete hasonló a következő példához:

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

Amikor az alkalmazás szünetel az üzenetek fogadása előtt, ellenőrizze a tárfiókot az [Azure Portalon.](https://portal.azure.com) Ellenőrizze, hogy az üzenetek a várólistában vannak-e.

Az **Üzenetek billentyűlenyomásaz** üzenetek fogadásához és törléséhez. Amikor a rendszer kéri, nyomja le ismét az **Enter** billentyűt a várólista törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre várólistát, és hogyan adhat hozzá üzeneteket Java-kód használatával. Ezután megtanulta betekinteni, beolvasni és törölni az üzeneteket. Végül megtanulta, hogyan kell törölni egy üzenetvárólistát.

Oktatóanyagok, minták, gyorsindítások és egyéb dokumentációk esetén látogasson el a következő oldalra:

> [!div class="nextstepaction"]
> [Azure Java-felhőfejlesztőknek](https://docs.microsoft.com/azure/developer/java/)

* További Azure Queue storage mintaalkalmazások megtekintéséhez folytassa az [Azure Queue storage SDK v12 Java-ügyfélkönyvtármintáival.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)
