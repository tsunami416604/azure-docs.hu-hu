---
title: 'Rövid útmutató: Azure Queue storage-tár v12 - .NET'
description: Ismerje meg, hogyan használhatja az Azure Queue .NET v12 függvénytár segítségével egy várólista létrehozásához és üzenetek hozzáadásához a várólistához. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket a várólistából. Azt is megtudhatja, hogyan törölhet idát várólistát.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/22/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: c69aa91596ff203445aa4fa3ccd59001ffe16649
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78197487"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Rövid útmutató: Azure Queue storage ügyfélkódtár v12 a .NET

Ismerkedés az Azure Queue storage ügyféltár 12-es verziójával. Az Azure Queue storage egy olyan szolgáltatás, amely nagy számú üzenetet tárol későbbi lekéréshez és feldolgozáshoz. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját.

> [!NOTE]
> Az előző SDK-verzió használatának megkezdéséhez olvassa el a [rövid útmutató: Az Azure Storage SDK-v11-es verziójának használata a .NET szolgáltatáshoz című témakört.](storage-quickstart-queues-dotnet-legacy.md)

A .NET-hez való Azure Queue storage ügyfélkódtár 12-es használatával:

* Üzenetsor létrehozása
* Üzenetek hozzáadása várólistához
* Betekintés a várólistában lévő üzenetekbe
* Üzenet frissítése a várólistában
* Üzenetek fogadása várólistából
* Üzenetek törlése várólistából
* Üzenetsor törlése

[API-referenciadokumentáció](/dotnet/api/azure.storage.queues) | [– könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues) | csomag[(NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0) | [mintái](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure storage-fiók – [tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Az operációs rendszer aktuális [.NET Core SDK-ja.](https://dotnet.microsoft.com/download/dotnet-core) Ügyeljen arra, hogy az SDK-t kapja, és ne a futásidőt.

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti a projekt előkészítése az Azure Queue storage ügyfélkódtár v12 a .NET.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *QueuesQuickstartV12*nevű .NET Core alkalmazást.

1. Egy konzolablakban (például cmd, PowerShell vagy `dotnet new` Bash) a paranccsal hozzon létre egy új konzolalkalmazást *QueuesQuickstartV12*névvel. Ez a parancs egy egyszerű "Hello World" C# projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Váltson az újonnan létrehozott *QueuesQuickstartV12* könyvtárra.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>A csomag telepítése

Amíg az alkalmazáskönyvtárban van, telepítse az Azure Queue storage `dotnet add package` ügyféltár .NET-csomag a parancs használatával.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Az alkalmazáskeretrendszer beállítása

A projekt könyvtárából:

1. A *Program.cs* fájl megnyitása a szerkesztőben
1. A `Console.WriteLine("Hello World!");` kimutatás eltávolítása
1. Direktívák hozzáadása `using`
1. A `Main` metódusdeklaráció frissítése az [aszinkronkód támogatásához](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



Itt a kód:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
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

Az alábbi .NET osztályok segítségével használhatja ezeket az erőforrásokat:

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient` A lehetővé teszi a tárfiók összes várólistájának kezelését.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): `QueueClient` Az osztály lehetővé teszi egy adott várólista és üzenetének kezelését és kezelését.
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): `QueueMessage` Az osztály a [receivemessage-ek](/dotnet/api/azure.storage.queues.queueclient.receivemessages) várólistán történő hívásakor visszaadott egyes objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek bemutatják, hogyan teheti meg a következő műveleteket az Azure Queue storage ügyfélkódtárban .NET esetén:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Üzenetsor létrehozása](#create-a-queue)
* [Üzenetek hozzáadása várólistához](#add-messages-to-a-queue)
* [Betekintés a várólistában lévő üzenetekbe](#peek-at-messages-in-a-queue)
* [Üzenet frissítése a várólistában](#update-a-message-in-a-queue)
* [Üzenetek fogadása várólistából](#receive-messages-from-a-queue)
* [Üzenetek törlése várólistából](#delete-messages-from-a-queue)
* [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati karakterláncát. A kapcsolati karakterlánc a [tárolási kapcsolati karakterlánc konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóban tárolódik.

Adja hozzá ezt `Main` a kódot a metódushoz:

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Üzenetsor létrehozása

Döntse el az új várólista nevét. Az alábbi kód hozzáfűz egy GUID értéket a várólista nevéhez, hogy megbizonyosodjon arról, hogy az egyedi.

> [!IMPORTANT]
> A várólistanevek csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődniük. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3 és 63 karakter közötti nek is kell lennie. A várólisták elnevezéséről további információt az [Elnevezési várólisták és a metaadatok](/rest/api/storageservices/naming-queues-and-metadata)című témakörben talál.


Hozzon létre egy példányt a [QueueClient](/dotnet/api/azure.storage.queues.queueclient) osztályból. Ezután hívja meg a [CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) metódust a tárfiókban lévő várólista létrehozásához.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása várólistához

A következő kódrészlet aszinkron módon [aSendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) metódus hívásával adja hozzá az üzeneteket a várólistához. Egy `SendMessageAsync` hívásból visszaadott [SendReceipt-ot](/dotnet/api/azure.storage.queues.models.sendreceipt) is ment. A visszaigazolás az üzenet későbbi frissítésére szolgál a program későbbi részében.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Betekintés a várólistában lévő üzenetekbe

A [PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) metódus hívásával tekintse be a várólistában lévő üzeneteket. A `PeekMessagesAsync` metódus egy vagy több üzenetet olvas le a várólista elejéről, de nem változtatja meg az üzenet láthatóságát.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Üzenet frissítése a várólistában

Az [UpdateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) metódus felhívásával frissítheti az üzenet tartalmát. A `UpdateMessageAsync` módszer módosíthatja az üzenetek láthatósági időtúlságát és tartalmát. Az üzenet tartalmának UTF-8 kódolású karakterláncnak kell lennie, amely legfeljebb 64 KB méretű. Az üzenet új tartalmával együtt adja át `SendReceipt` a kód korábbi részében mentett értékeket. Az `SendReceipt` értékek azonosítják a frissítandó üzenetet.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása várólistából

Töltse le a korábban hozzáadott üzeneteket a [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) metódus felhívásával.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Üzenetek törlése várólistából

A feldolgozás után törölje az üzeneteket a várólistából. Ebben az esetben a feldolgozás csak az üzenet megjelenítése a konzolon.

Az alkalmazás szünetel a `Console.ReadLine` felhasználói bevitel hez, ha feldolgozza és törli az üzeneteket, és feldolgozza és törli a felhasználókat. Ellenőrizze az [Azure Portalon,](https://portal.azure.com) hogy az erőforrások megfelelően jöttek-e létre, mielőtt törölné őket. A kifejezetten nem törölt üzenetek végül ismét láthatóvá válnak a várólistában, hogy újabb esélyt kaphassanak azok feldolgozására.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Üzenetsor törlése

A következő kód törli az okat az erőforrásokat, amelyeket az alkalmazás a [Várólista DeleteAsync](/dotnet/api/azure.storage.queues.queueclient.deleteasync) metódussal történő törlésével hozott létre.

Adja hozzá ezt a `Main` kódot a módszer végéhez:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre, és három üzenetet ad hozzá egy Azure-várólistához. A kód felsorolja az üzeneteket a várólistában, majd beolvassa és törli őket, mielőtt véglegesen törli a várólistát.

A konzolablakban keresse meg az alkalmazáskönyvtárat, majd építse fel és futtassa az alkalmazást.

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás kimenete hasonló a következő példához:

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Amikor az alkalmazás szünetel az üzenetek fogadása előtt, ellenőrizze a tárfiókot az [Azure Portalon.](https://portal.azure.com) Ellenőrizze, hogy az üzenetek a várólistában vannak-e.

Az **Üzenetek billentyűlenyomásaz** üzenetek fogadásához és törléséhez. Amikor a rendszer kéri, nyomja le ismét az **Enter** billentyűt a várólista törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre várólistát, és hogyan adhat hozzá üzeneteket aszinkron .NET kód használatával. Ezután megtanulta betekinteni, beolvasni és törölni az üzeneteket. Végül megtanulta, hogyan kell törölni egy üzenetvárólistát.

Oktatóanyagok, minták, gyorsindítások és egyéb dokumentációk esetén látogasson el a következő oldalra:

> [!div class="nextstepaction"]
> [Azure .NET- és .NET Core-fejlesztőknek](https://docs.microsoft.com/dotnet/azure/)

* További információ: Azure [Storage-tárak a .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage)
* További Azure Queue storage mintaalkalmazások megtekintéséhez folytassa az [Azure Queue storage v12 .NET ügyfélkódtár-mintáival.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)
* A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
