---
title: 'Gyors útmutató: Üzenetsor létrehozása az Azure Storage-ban a .NET használatával'
description: Ez a rövid útmutatóban megismerheti, hogyan hozzon létre egy üzenetsort, és üzeneteket ad hozzá, az Azure Storage ügyféloldali kódtára a .NET használatával. Ezután megismerheti, hogyan olvassa el, és a várólistában lévő üzenetek feldolgozásához.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/06/2018
ms.author: tamram
ms.openlocfilehash: f16c4438dfb2feb70dece0b95f8afc701c5a3d66
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009308"
---
# <a name="quickstart-use-net-to-create-a-queue-in-azure-storage"></a>Gyors útmutató: Üzenetsor létrehozása az Azure Storage-ban a .NET használatával

Ez a rövid útmutatóban megismerheti, hogyan hozzon létre egy üzenetsort, és üzeneteket ad hozzá, az Azure Storage ügyféloldali kódtára a .NET használatával. Ezután megismerheti, hogyan olvassa el, és a várólistában lévő üzenetek feldolgozásához. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ezután töltse le és telepítse a .NET Core 2.0 az operációs rendszernek megfelelő verzióját. Ha Windows rendszert használ, igény szerint telepítheti a Visual Studiót és használhatja a .NET-keretrendszert is. Emellett telepíthet egy, az operációs rendszerrel használható szerkesztőprogramot is.

### <a name="windows"></a>Windows

- A [.NET Core for Windows](https://www.microsoft.com/net/download/windows) vagy a [.NET-keretrendszer](https://www.microsoft.com/net/download/windows) telepítése (a Windows rendszerhez készült Visual Studio része)
- Telepítse [a Windowshoz készült Visual Studio programot](https://www.visualstudio.com/). Ha .NET Core keretrendszert használ, a Visual Studio telepítése nem kötelező.  

A .NET Core és a .NET-keretrendszer közötti választással kapcsolatos további információért lásd a [.NET Core és a .NET-keretrendszer közötti, kiszolgálóalkalmazások esetén való választásról](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) szóló részt.

### <a name="linux"></a>Linux

- Telepítse a [.NET Core for Linux keretrendszert](https://www.microsoft.com/net/download/linux).
- Emellett telepítheti [a Visual Studio Code programot](https://www.visualstudio.com/) és a [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068) is.

### <a name="macos"></a>macOS

- Telepítse a [.NET Core for macOS keretrendszert](https://www.microsoft.com/net/download/macos).
- Emellett telepítheti [a Visual Studio for Mac programot](https://www.visualstudio.com/vs/visual-studio-mac/) is.

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt mintaalkalmazás egy egyszerű konzolalkalmazás. A mintaalkalmazást megismerheti a [GitHubon](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

A [git](https://git-scm.com/) használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A Visual Studio-megoldás megnyitásához keresse meg a *tároló-üzenetsorok-dotnet-quickstart* mappában nyissa meg, és kattintson duplán a *tároló-üzenetsorok-dotnet-quickstart.sln fájlra*. 

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

A mintaalkalmazás létrehoz egy üzenetsort, és a egy üzenetet ad hozzá. Az alkalmazás első betekintés a várólistából történő eltávolítása nélkül, majd lekéri az üzenet, és törli az üzenetsorból.

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

A mintában először ellenőrzi, hogy a környezeti változó létrehozásához elemezhető kapcsolati karakterláncot tartalmaz egy [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) objektum a tárfiókra mutat. Annak ellenőrzéséhez, hogy érvényes-e a kapcsolati karakterláncot, a példa a [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) metódust. Ha a **TryParse** sikeres, a minta inicializálja a *storageAccount* változót, és **igaz** értéket ad vissza.

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

Először a minta létrehoz egy üzenetsort, és egy üzenetet ad hozzá. 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Üzenet hozzáadása

A minta ezután egy üzenetet ad hozzá a várólista-jének. 

Egy üzenet, amely tartalmazhat egy XML-kérelem, az UTF-8 kódolást, és lehet, hogy legfeljebb 64 KB méretű formátumban kell lennie. Egy üzenet bináris adatokat tartalmaz, akkor a Microsoft azt javasolja, hogy Ön a Base64 kódolás az üzenetet.

Alapértelmezés szerint a time-to-live üzenet maximális értéke 7 nap. Megadhat bármilyen pozitív szám, az üzenet time-to-live, valamint a-1 érték azt jelzik, hogy az üzenet nem jár le.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

### <a name="peek-a-message-from-the-queue"></a>Belepillantás egy üzenetet az üzenetsorból

A minta bemutatja, hogyan Belepillantás egy üzenetet az üzenetsorból. Amikor egy üzenet, megtekintése, az üzenet tartalmát olvashat. Azonban az üzenet is látható marad más ügyfelek számára, hogy egy másik ügyfél ezt követően beolvashatja és feldolgozni az üzenetet.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Egy üzenet eltávolítása a sorból

A minta azt is bemutatja, hogyan egy üzenet eltávolítása a sorból. Eltávolítása a sorból egy üzenetet, ha az üzenet lekérése a várólista elejére, és átmenetileg nem látható megjelenítési más ügyfelek számára. Alapértelmezés szerint egy üzenet marad láthatatlan 30 másodpercig. Ebben az időszakban a kódot is feldolgozni az üzenetet. Az üzenet dequeueing befejezéséhez törli az üzenetet a feldolgozás után azonnal úgy, hogy egy másik ügyfél nem eltávolítása a sorból ugyanazt az üzenetet.

Ha a kód nem tudja feldolgozni egy üzenetet, mert egy hardveres vagy szoftveres hiba, majd az üzenet után válik láthatóvá újra láthatatlansági időszak lejárt. Egy másik ügyfél is ugyanazt az üzenetet lekérni, és próbálkozzon újra.

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

A minta üzenetfájlok törlése a várólista által létrehozott erőforrásokat. A várólista törlése is törli az olyan üzeneteket tartalmaz.

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

## <a name="resources-for-developing-net-applications-with-queues"></a>Az üzenetsorok .NET-alkalmazások fejlesztéséhez használható forrásanyagok

Tekintse meg az alábbi további forrásanyagokat .NET-fejlesztés az Azure-üzenetsorok:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- Az Azure Storage-hoz készült [.NET ügyféloldali kódtár](https://www.nuget.org/packages/WindowsAzure.Storage/) legújabb verziójáért töltse le a NuGet-csomagot. 
- A [.NET ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-net) a GitHubon tekintheti meg.

### <a name="client-library-reference-and-samples"></a>Ügyféloldali kódtár – referencia és minták

- A .NET ügyféloldali kódtárral kapcsolatos további információért lásd a [.NET API-referenciáját](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Ismerkedés a [Queue storage-mintákat](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) a .NET ügyféloldali kódtár használatával írt.

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan üzeneteket ad hozzá egy üzenetsorba, a betekintési üzeneteket az üzenetsorból, és a sorból, és a .NET-tel-üzenetek feldolgozása. 

> [!div class="nextstepaction"]
> [Azure Queue Storage alkalmazások közötti kommunikációhoz](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
