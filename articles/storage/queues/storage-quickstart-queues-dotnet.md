---
title: 'Rövid útmutató: Azure üzenetsor Storage Library v12-.NET'
description: Megtudhatja, hogyan használhatja az Azure üzenetsor .NET V12 függvénytárát üzenetsor létrehozásához és üzenetek a várólistához való hozzáadásához. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket a várólistából. Azt is megtudhatja, hogyan törölhet egy várólistát.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/22/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 69e2c118ec298ebddf16d428ea2278de42e91309
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036692"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Gyors útmutató: Azure üzenetsor Storage ügyféloldali kódtára a .NET-hez

Ismerkedjen meg az Azure üzenetsor Storage ügyféloldali kódtár 12-es verziójával a .NET-hez. Az Azure üzenetsor-tároló egy olyan szolgáltatás, amely nagy mennyiségű üzenetet tárol a későbbi lekérésekhez és feldolgozáshoz. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja a programkódot az alapszintű feladatokhoz.

> [!NOTE]
> A korábbi SDK-verzió használatának megkezdéséhez tekintse meg a gyors üzembe helyezési útmutató [: a .net-hez készült Azure Storage SDK-v11 használata a várólista kezeléséhez](storage-quickstart-queues-dotnet-legacy.md).

Használja az Azure üzenetsor Storage ügyféloldali kódtárat a .NET-hez a következőhöz:

* Üzenetsor létrehozása
* Üzenetek hozzáadása egy várólistához
* Üzenetek betekintése egy várólistába
* Üzenetsor frissítése
* Üzenetek fogadása egy várólistából
* Üzenetek törlése egy várólistából
* Üzenetsor törlése

További források:

* [API-referenciadokumentáció](/dotnet/api/azure.storage.queues)
* [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
* [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
* [Példák](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Az operációs rendszer jelenlegi [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) . Győződjön meg arról, hogy az SDK-t és nem a futtatókörnyezetet kapja meg.

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti a projekt előkészítésének folyamatán az Azure üzenetsor Storage ügyféloldali kódtára a .NET-hez való működéséhez.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *QueuesQuickstartV12*nevű .net Core-alkalmazást.

1. A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, *QueuesQuickstartV12*nevű Console-alkalmazást. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: *program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Váltson az újonnan létrehozott *QueuesQuickstartV12* -könyvtárra.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>A csomag telepítése

Az alkalmazás könyvtárában a parancs használatával telepítse az Azure üzenetsor Storage ügyféloldali kódtárat a .NET-csomaghoz `dotnet add package` .

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Nyissa meg a *program.cs* fájlt a szerkesztőben
1. Az `Console.WriteLine("Hello World!");` utasítás eltávolítása
1. `using`Irányelvek hozzáadása
1. A `Main` metódus deklarációjának frissítése az [aszinkron kód támogatásához](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



A kód a következő:

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

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra. A várólista-tárolás háromféle típusú erőforrást kínál:

* A Storage-fiók
* A Storage-fiókban lévő üzenetsor
* Üzenetek a várólistán belül

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Üzenetsor-tárolási architektúra ábrája](./media/storage-queues-introduction/queue1.png)

Használja az alábbi .NET-osztályokat a következő erőforrásokkal való interakcióhoz:

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): a `QueueServiceClient` lehetővé teszi a Storage-fiók összes várólistájának kezelését.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): a `QueueClient` osztály segítségével kezelheti és kezelheti az egyes várólistákat és azok üzeneteit.
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): az `QueueMessage` osztály a [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) egy várólistán való meghívásakor visszaadott egyedi objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő műveleteket az Azure üzenetsor-tároló .NET-hez készült ügyféloldali kódtára használatával:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Üzenetsor létrehozása](#create-a-queue)
* [Üzenetek hozzáadása egy várólistához](#add-messages-to-a-queue)
* [Üzenetek betekintése egy várólistába](#peek-at-messages-in-a-queue)
* [Üzenetsor frissítése](#update-a-message-in-a-queue)
* [Üzenetek fogadása egy várólistából](#receive-messages-from-a-queue)
* [Üzenetek törlése egy várólistából](#delete-messages-from-a-queue)
* [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage-fiókhoz tartozó kapcsolatok karakterláncot kérdezi le. A hálózati karakterláncot a [Storage-kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóban tárolja a rendszer.

Adja hozzá ezt a kódot a `Main` metódushoz:

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

Döntse el az új üzenetsor nevét. Az alábbi kód egy GUID értéket fűz hozzá a várólista neveként, hogy az egyedi legyen.

> [!IMPORTANT]
> A várólisták nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3 – 63 karakter hosszúnak kell lennie. További információ a várólisták elnevezéséről: [várólisták és metaadatok elnevezése](/rest/api/storageservices/naming-queues-and-metadata).


Hozza létre a [QueueClient](/dotnet/api/azure.storage.queues.queueclient) osztály egy példányát. Ezután hívja meg a [CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) metódust az üzenetsor létrehozásához a Storage-fiókban.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

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

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása egy várólistához

A következő kódrészlet aszinkron módon adja hozzá az üzeneteket a várólistához a [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) metódus meghívásával. Egy hívásból visszaadott [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) is ment `SendMessageAsync` . A visszaigazolás az üzenet későbbi, a programban való frissítésére szolgál.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Üzenetek betekintése egy várólistába

A [PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) metódus meghívásával megtekintheti az üzenetsor üzeneteit. A `PeekMessagesAsync` metódus egy vagy több üzenetet kérdez le a várólista elejéről, de nem módosítja az üzenet láthatóságát.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

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

### <a name="update-a-message-in-a-queue"></a>Üzenetsor frissítése

Egy üzenet tartalmának frissítéséhez hívja meg a [UpdateMessageAsync](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) metódust. A `UpdateMessageAsync` metódus módosíthatja az üzenet láthatósági időtúllépését és tartalmát. Az üzenet tartalmának UTF-8 kódolású karakterláncnak kell lennie, amely akár 64 KB méretű is lehet. Az üzenet új tartalma mellett adja meg a `SendReceipt` korábban a kódban mentett értékeket. Az `SendReceipt` értékek határozzák meg, hogy melyik üzenetet kell frissíteni.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából

A korábban hozzáadott üzenetek letöltése a [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) metódus meghívásával.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Üzenetek törlése egy várólistából

Üzenetek törlése a sorból a feldolgozás után. Ebben az esetben a feldolgozás csak az üzenet megjelenítését jeleníti meg a konzolon.

Az alkalmazás a felhasználói bevitel szüneteltetését hívja `Console.ReadLine` meg, mielőtt feldolgozza és törli az üzeneteket. A törlés előtt ellenőrizze, hogy az erőforrások megfelelően lettek-e létrehozva a [Azure Portal](https://portal.azure.com) . A nem explicit módon törölt üzenetek később ismét láthatóvá válnak a várólistában, hogy egy másik lehetőség is feldolgozza őket.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

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

A következő kód megtisztítja az alkalmazás által létrehozott erőforrásokat az üzenetsor törlésével a [DeleteAsync](/dotnet/api/azure.storage.queues.queueclient.deleteasync) metódus használatával.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre és tesz hozzá egy Azure-várólistához. A kód felsorolja a várólistán lévő üzeneteket, majd lekéri és törli őket, mielőtt véglegesen törölné a várólistát.

A konzol ablakban navigáljon az alkalmazás könyvtárába, majd hozza létre és futtassa az alkalmazást.

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás kimenete az alábbi példához hasonló:

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

Ha az alkalmazás szünetelteti az üzenetek fogadását, akkor a [Azure Portalban](https://portal.azure.com)keresse meg a Storage-fiókját. Ellenőrizze, hogy az üzenetek szerepelnek-e a várólistán.

Nyomja le az **ENTER** billentyűt az üzenetek fogadásához és törléséhez. Ha a rendszer kéri, nyomja meg ismét az **ENTER** billentyűt a várólista törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre üzenetsor-t, és hogyan adhat hozzá üzeneteket az aszinkron .NET-kód használatával. Ezután megtanulta az üzenetek betekintését, beolvasását és törlését. Végezetül megtanulta, hogyan törölhet egy üzenetsor-várólistát.

Oktatóanyagok, minták, gyors indítás és egyéb dokumentáció:

> [!div class="nextstepaction"]
> [Azure .NET- és .NET Core-fejlesztőknek](https://docs.microsoft.com/dotnet/azure/)

* További információt a [.net-hez készült Azure Storage kódtárak](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage)című témakörben talál.
* További Azure üzenetsor-tárolási minta alkalmazások megjelenítéséhez folytassa az [Azure üzenetsor Storage v12 .net Ügyféloldali függvénytárának mintáit](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
* A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
