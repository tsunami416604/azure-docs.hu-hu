---
title: 'Rövid útmutató: A .NET-hez való Azure Storage v11-es használata egy várólista kezeléséhez'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Azure Storage-ügyfélkódtár .NET egy várólista létrehozásához és üzenetek hozzáadása. Ezután megtudhatja, hogyan olvashatja és dolgozhatja fel az üzeneteket a várólistából.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/06/2018
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: c327629f0c5e88520a8bb0b9c4ff68e6edc00c35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79137339"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>Rövid útmutató: A .NET-hez való Azure Storage SDK v11-es használatával kezelheti a várólistát

Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Azure Storage-ügyfélkódtár 11-es verziója a .NET-hez egy várólista létrehozásához és üzenetek hozzáadásához. Ezután megtudhatja, hogyan olvashatja és dolgozhatja fel az üzeneteket a várólistából. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ezután töltse le és telepítse a .NET Core 2.0 az operációs rendszernek megfelelő verzióját. Ha Windows rendszert használ, igény szerint telepítheti a Visual Studiót és használhatja a .NET-keretrendszert is. Emellett telepíthet egy, az operációs rendszerrel használható szerkesztőprogramot is.

### <a name="windows"></a>Windows

- A [.NET Core for Windows](https://www.microsoft.com/net/download/windows) vagy a [.NET-keretrendszer](https://www.microsoft.com/net/download/windows) telepítése (a Windows rendszerhez készült Visual Studio része)
- Telepítse [a Windowshoz készült Visual Studio programot](https://www.visualstudio.com/). Ha .NET Core keretrendszert használ, a Visual Studio telepítése nem kötelező.  

A .NET Core és a .NET-keretrendszer közötti választással kapcsolatos további információért lásd a [.NET Core és a .NET-keretrendszer közötti, kiszolgálóalkalmazások esetén való választásról](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) szóló részt.

### <a name="linux"></a>Linux

- Telepítse a [.NET Core for Linux keretrendszert](https://www.microsoft.com/net/download/linux).
- Emellett telepítheti [a Visual Studio Code programot](https://www.visualstudio.com/) és a [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is.

### <a name="macos"></a>macOS

- Telepítse a [.NET Core for macOS keretrendszert](https://www.microsoft.com/net/download/macos).
- Emellett telepítheti [a Visual Studio for Mac programot](https://www.visualstudio.com/vs/visual-studio-mac/) is.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt mintaalkalmazás egy egyszerű konzolalkalmazás. A mintaalkalmazást megismerheti a [GitHubon](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

A [git](https://git-scm.com/) segítségével töltse le az alkalmazás egy példányát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A Visual Studio-megoldás megnyitásához keresse meg a *storage-queues-dotnet-quickstart mappát,* nyissa meg, és kattintson duplán a *storage-queues-dotnet-quickstart.sln fájlra.* 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

Az alkalmazás futtatásához meg kell adnia a tárfiókjához tartozó kapcsolati sztringet. A mintaalkalmazás beolvassa a kapcsolati sztringet egy környezeti változóból, és annak használatával engedélyezi az Azure Storage számára küldött kéréseket.

A kapcsolati sztring másolása után írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje le a `<yourconnectionstring>` kifejezést a tényleges kapcsolati sztringre:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Miután hozzáadta a környezeti változót, előfordulhat, hogy újra kell indítania minden futó programot, amelynek szüksége lehet a környezeti változó beolvasására, beleértve a konzolablakot is. Ha például Visual Studiót használt szerkesztőként, indítsa újra a minta futtatása előtt. 

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

### <a name="macos"></a>macOS

Módosítsa a .bash_profile fájlt, és adja hozzá a környezeti változót:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

## <a name="run-the-sample"></a>Minta futtatása

A mintaalkalmazás létrehoz egy várólistát, és hozzáad egy üzenetet. Az alkalmazás először úgy tekint be az üzenetbe, hogy nem távolítja el a várólistából, majd lekéri az üzenetet, és törli azt a várólistából.

### <a name="windows"></a>Windows

Ha Visual Studiót használt szerkesztőként, a futtatáshoz nyomja le az **F5** billentyűt. 

Máskülönben lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

### <a name="linux"></a>Linux

Lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

### <a name="macos"></a>macOS

Lépjen az alkalmazás könyvtárába, majd futtassa az alkalmazást a `dotnet run` paranccsal.

```
dotnet run
```

A mintaalkalmazás kimenete az alábbihoz hasonló lesz:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="try-parsing-the-connection-string"></a>Kapcsolati sztring elemzése

A minta először ellenőrzi, hogy a környezeti változó tartalmaz-e kapcsolati karakterláncot, amely elemezhető a storage-fiókra mutató [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) objektum létrehozásához. Annak ellenőrzéséhez, hogy a kapcsolati karakterlánc érvényes-e, a minta a [TryParse metódust](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) használja. Ha a **TryParse** sikeres, a minta inicializálja a *storageAccount* változót, és **igaz** értéket ad vissza.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...    
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Az üzenetsor létrehozása

Először a minta létrehoz egy várólistát, és hozzáad egy üzenetet. 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Üzenet hozzáadása

Ezután a minta hozzáad egy üzenetet a várólista hátuljához. 

Az üzenetnek utf-8 kódolású XML-kérelemben olyan formátumban kell lennie, amely legfeljebb 64 KB méretű lehet. Ha egy üzenet bináris adatokat tartalmaz, javasoljuk, hogy a Base64-kódolja az üzenetet.

Alapértelmezés szerint az üzenet maximális ideig tart-ig 7 nap. Az üzenethez az élő höz bármilyen pozitív számot megadhat.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Ha olyan üzenetet szeretne hozzáadni, `Timespan.FromSeconds(-1)` amely nem jár le, használja az [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync)hívásban.

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Üzenet betekintése a várólistából

A minta bemutatja, hogyan lehet betekinteni egy üzenetet egy várólistából. Amikor betekint egy üzenetbe, elolvashatja az üzenet tartalmát. Az üzenet azonban látható marad a többi ügyfél számára, így egy másik ügyfél később lekérheti és feldolgozhatja az üzenetet.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Üzenet eltávolítása az üzenetsorból

A minta azt is bemutatja, hogyan lehet egy üzenet várólistájának törlését. Amikor dequeue egy üzenetet, akkor beolvasni az üzenetet az első a várólista, és ideiglenesen láthatatlanná teszi a többi ügyfél számára. Alapértelmezés szerint az üzenet 30 másodpercig láthatatlan marad. Ez alatt az idő alatt a kód feltudja dolgozni az üzenetet. Az üzenet várólistájának befejezéséhez a feldolgozás után azonnal törölje az üzenetet, így egy másik ügyfél ne vonja vissza ugyanazt az üzenetet.

Ha a kód hardver- vagy szoftverhiba miatt nem tudja feldolgozni az üzenetet, akkor az üzenet a láthatatlanság időszaka lejárta után ismét láthatóvá válik. Egy másik ügyfél lekérheti ugyanazt az üzenetet, és újra próbálkozhat.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for 
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A minta törli a várólista törlésével létrehozott erőforrásokat. A várólista törlésével a benne lévő üzenetek is törlődnek.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>.NET-alkalmazások várólistával való fejlesztéséhez szükséges erőforrások

Tekintse meg ezeket a további erőforrásokat a .NET fejlesztéshez az Azure-várólisták segítségével:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- Töltse le a NuGet csomagokat az [Azure Storage ügyfélkönyvtárának legújabb verziójához.](/dotnet/api/overview/azure/storage?view=azure-dotnet)
    - [Közös](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
    - [Üzenetsorok](https://www.nuget.org/packages/Azure.Storage.Queues/)
- A [.NET ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-net) a GitHubon tekintheti meg.

### <a name="client-library-reference-and-samples"></a>Ügyféloldali kódtár – referencia és minták

- A .NET ügyféloldali kódtárral kapcsolatos további információért lásd a [.NET API-referenciáját](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Fedezze fel a .NET ügyféltár használatával írt [várólista-tárolási mintákat.](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan adhat üzeneteket egy várólistához, hogyan tekintheti meg a várólistából érkező üzeneteket, és hogyan vonhatja vissza az üzenetek várólistáját és feldolgozását a .NET használatával. 

> [!div class="nextstepaction"]
> [Kommunikáció az alkalmazások között az Azure Queue storage szolgáltatással](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
