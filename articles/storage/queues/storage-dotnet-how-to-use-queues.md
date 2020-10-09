---
title: Az Azure üzenetsor-tárolás használatának első lépései a .NET-Azure Storage használatával
description: Az Azure-üzenetsorok megbízható, aszinkron üzenetkezelést biztosítanak az alkalmazások összetevői között. A felhőbeli üzenetkezelésnek köszönhetően az alkalmazások összetevői függetlenül méretezhetők.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/08/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: c07ad6e631482b47da674549e976953842cf983e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855922"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Áttekintés

Az Azure Queue Storage felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások tervezésekor az alkalmazás-összetevők gyakran le vannak választva, így egymástól függetlenül méretezhetők. A várólista-tároló aszinkron üzenetküldést biztosít az alkalmazás-összetevők között, függetlenül attól, hogy a felhőben, az asztalon, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése

Ez az oktatóanyag bemutatja, hogyan írhat .NET-kódot néhány, az Azure Queue Storage szolgáltatást használó általános forgatókönyvhöz. Az ismertetett forgatókönyvek az üzenetsorok létrehozására és törlésére, valamint az üzenetsor üzeneteinek hozzáadására, olvasására és törlésére vonatkoznak.

**Az oktatóanyag áttekintésének becsült ideje:** 45 perc

### <a name="prerequisites"></a>Előfeltételek

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [Azure Storage-fiók](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A következő lépésként állítsa be a fejlesztési környezetet a Visual Studióban, hogy kipróbálhassa az útmutatóban megadott példákat.

### <a name="create-a-windows-console-application-project"></a>Windows-konzolalkalmazás projekt létrehozása

Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. A következő lépések bemutatják, hogyan hozhat létre egy Console-alkalmazást a Visual Studio 2019-ben. A lépések a Visual Studio más verziói esetén is hasonlók.

1. Válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.
2. **Platform**  >  **Windows** kiválasztása
3. A **konzol alkalmazás kiválasztása (.NET-keretrendszer)**
4. Kattintson a **Tovább** gombra.
5. A **projekt neve** mezőben adja meg az alkalmazás nevét
6. Kattintson a **Létrehozás** elemre.

Az oktatóanyagban szereplő összes példa a konzol alkalmazás **program.cs** fájljának **Main ()** metódusához adható hozzá.

Az Azure Storage ügyféloldali kódtárait bármilyen típusú .NET-alkalmazásban használhatja, beleértve az Azure Cloud Service-t vagy a webalkalmazást, valamint az asztali és mobil alkalmazásokat is. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

### <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

A következő négy csomagra kell hivatkoznia a projektben az oktatóanyag elvégzéséhez:

- A [.net-hez készült Azure Core Library](https://www.nuget.org/packages/Azure.Core/): Ez a csomag megosztott primitíveket, absztraktokat és segítőket biztosít a modern .net Azure SDK ügyféloldali kódtárak számára.
- Az [Azure Storage közös ügyféloldali kódtára a .net-hez](https://www.nuget.org/packages/Azure.Storage.Common/): Ez a csomag a többi Azure Storage ügyféloldali kódtára által megosztott infrastruktúrát biztosít.
- [Azure Storage üzenetsor-függvénytár a .net-hez](https://www.nuget.org/packages/Azure.Storage.Queues/): Ez a csomag lehetővé teszi az azure Storage Queue szolgáltatás használatát az ügyfél által elérhető üzenetek tárolásához.
- A [.net-hez készült Configuration Manager kódtára](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): Ez a csomag hozzáférést biztosít az ügyfélalkalmazások konfigurációs fájljaihoz.

A NuGet a csomagok beszerzéséhez használható. Kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektre **megoldáskezelő**, majd válassza a **NuGet-csomagok kezelése**lehetőséget.
1. **Tallózás** kiválasztása
1. Keressen rá az interneten az "Azure. Storage. Queues" kifejezésre, és válassza a **telepítés** lehetőséget a Storage ügyféloldali kódtár és függőségeinek telepítéséhez. Ez az Azure. Storage. Common és az Azure. Core kódtárakat is telepíti, amelyek a várólista-függvénytár függőségei.
1. Keressen rá az interneten a "System.Configuration.ConfigurationManager" kifejezésre, és válassza a **telepítés** lehetőséget a Configuration Manager telepítéséhez.

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

A következő három csomagra kell hivatkoznia a projektben az oktatóanyag elvégzéséhez:

- A [.net-hez készült általános ügyféloldali kódtár Microsoft Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Ez a csomag programozott hozzáférést biztosít a Storage-fiókban lévő adaterőforrásokhoz.
- [Microsoft Azure Storage üzenetsor-függvénytár a .net-hez](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): ez az ügyféloldali kódtár lehetővé teszi a Microsoft Azure Storage Queue szolgáltatás használatát az ügyfél által elérhető üzenetek tárolásához.
- [A Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): Ez a csomag egy osztályt biztosít a konfigurációs fájlban található kapcsolati sztring elemzéséhez, függetlenül attól, hogy az alkalmazás hol fut.

A NuGet a csomagok beszerzéséhez használható. Kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektre **megoldáskezelő**, majd válassza a **NuGet-csomagok kezelése**lehetőséget.
1. **Tallózás** kiválasztása
1. Keressen rá az interneten a "Microsoft. Azure. Storage. üzenetsor" kifejezésre, és válassza a **telepítés** lehetőséget a Storage ügyféloldali kódtár és függőségeinek telepítéséhez. Ez a Microsoft. Azure. Storage. Common könyvtárat is telepíti, amely a várólista-függvénytár függősége.
1. Keressen rá az interneten a "Microsoft.Azure.ConfigurationManager" kifejezésre, és válassza a **telepítés** lehetőséget az Azure-Configuration Manager telepítéséhez.

---

### <a name="determine-your-target-environment"></a>A célkörnyezet meghatározása

Az útmutatóban lévő példákat kétféle környezetben futtathatja:

- A kódot futtathatja a felhőben, egy Azure Storage-fiókban.
- A kódot a Azurite Storage Emulator használatával futtathatja. A Azurite egy helyi környezet, amely egy Azure Storage-fiókot emulál a felhőben. A Azurite egy ingyenes lehetőség a kód tesztelésére és hibakeresésére, miközben az alkalmazás fejlesztés alatt áll. Az emulátor egy jól ismert fiókot és kulcsot használ. További információ: [a Azurite Emulator használata a helyi Azure Storage-fejlesztéshez és-teszteléshez](../common/storage-use-azurite.md).

> [!NOTE]
> A Storage Emulator megcélzásával elkerülheti az Azure Storage-hoz kapcsolódó költségeket. Ha azonban mégis egy Azure Storage-fiókot céloz meg a felhőben, az oktatóanyag végrehajtásával járó költségek elhanyagolhatóak.

## <a name="get-your-storage-connection-string"></a>A Storage-beli kapcsolatok karakterláncának beolvasása

Az Azure Storage ügyféloldali kódtárai a .NET-támogatáshoz egy Storage-kapcsolódási karakterlánc használatával konfigurálja a végpontokat és a hitelesítő adatokat a tárolási szolgáltatások eléréséhez. További információ: a [Storage-fiók elérési kulcsainak kezelése](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

A mintakódnak hitelesítenie kell a tárfiókhoz való hozzáférést. A hitelesítéshez meg kell adni az alkalmazás számára a tárfiók hitelesítő adatait egy kapcsolati sztring formájában. A tárfiók hitelesítő adatainak megtekintéséhez a következőt kell tennie:

1. Navigáljon a [Azure Portal](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati sztringek.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

A kapcsolati sztringekkel kapcsolatos további információkért lásd: [Az Azure Storage kapcsolati sztringjének konfigurálása](../common/storage-configure-connection-string.md).

> [!NOTE]
> A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Mindig ügyeljen a tárfiók kulcsának védelmére. Ne adja ki másoknak, ne kódolja fixen és ne mentse egy mások számára elérhető egyszerű szöveges fájlban. Ha azt gyanítja, hogy a kulcs biztonsága sérült, az Azure portál segítségével generálja újra.

A tárolási kapcsolati sztring egy konfigurációs fájlban tartható fenn a legjobban. A kapcsolódási karakterlánc konfigurálásához nyissa meg a *app.config* fájlt megoldáskezelő a Visual Studióban. Adja hozzá az alábbi `\<appSettings\>` elem tartalmát. Cserélje le a *-karakterláncot* a Storage-fiókból másolt értékre a portálon:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

A konfiguráció beállítása például hasonló lesz a következőhöz:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

A Azurite-tároló emulátorának megcélzásához használhatja a jól ismert fiók nevét és kulcsát leképező parancsikont. Ebben az esetben a kapcsolati sztring beállítása a következő:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Hozzáadás irányelvekkel

Adja hozzá a következő `using` irányelveket a `Program.cs` fájl elejéhez:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-service-client"></a>A Queue szolgáltatásügyfél létrehozása

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

A [QueueClient](/dotnet/api/azure.storage.queues.queueclient) osztály lehetővé teszi a Queue Storage-ban tárolt várólisták lekérését. A szolgáltatásügyfél létrehozásának egyik módja:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

A [CloudQueueClient](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) osztály segítségével lekérheti a Queue Storage-ban tárolt üzenetsorokat. A szolgáltatásügyfél létrehozásának egyik módja:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Most már készen áll a Queue Storage-ból adatokat olvasó és abba adatokat író kód írására.

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Ez a példa egy üzenetsor létrehozását mutatja be:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Üzenet beszúrása egy üzenetsorba

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Ha egy üzenetet egy meglévő várólistába szeretne beszúrni, hívja meg a [üzenetküldés](/dotnet/api/azure.storage.queues.queueclient.sendmessage) metódust. Egy üzenet lehet egy `string` (UTF-8 formátumban) vagy egy `byte` tömb. A következő kód létrehoz egy várólistát (ha még nem létezik), és beszúr egy üzenetet:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

Ha üzenetet szeretne beszúrni egy létező üzenetsorba, először hozzon létre egy [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) elemet. Ezután hívja meg az [AddMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) módszert. A `CloudQueueMessage` létrehozható egy `string` (UTF-8 formátumú) vagy egy `byte` tömbben. Az alábbi kód létrehoz egy várólistát (ha nem létezik), és beszúrja a "Hello, World" üzenetet:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Betekintés a következő üzenetbe

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

A [PeekMessages](/dotnet/api/azure.storage.queues.queueclient.peekmessages) metódus meghívásával megtekintheti a várólistán lévő üzeneteket anélkül, hogy el kellene távolítani őket a várólistából. Ha nem adja meg a *maxMessages* paraméter értékét, az alapértelmezett érték egy üzenet bepillantása.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

Egy üzenetsor elején található üzenetbe anélkül is bepillanthat, hogy eltávolítaná az üzenetsorból. Ehhez hívja meg a [PeekMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) módszert.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása

Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="de-queue-the-next-message"></a>A következő üzenet kivétele az üzenetsorból

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Üzenetsor-várólistán lévő üzenet két lépésben. A [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages)meghívásakor a következő üzenet jelenik meg egy várólistában. A visszaadott üzenet a `ReceiveMessages` várólistából beolvasott más kódokba is láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Ha végleg el szeretné távolítani az üzenetet az üzenetsorból, meg kell hívnia a [DeleteMessage](/dotnet/api/azure.storage.queues.queueclient.deletemessage) módszert is. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód meghívása `DeleteMessage` közvetlenül az üzenet feldolgozása után történik.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

A kód két lépésben távolít el egy üzenetet az üzenetsorból. A [GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) meghívásával lekéri az üzenetsor következő üzenetét. A visszaadott üzenet a `GetMessage` várólistából beolvasott más kódokba is láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Ha végleg el szeretné távolítani az üzenetet az üzenetsorból, meg kell hívnia a [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) módszert is. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód meghívása `DeleteMessage` közvetlenül az üzenet feldolgozása után történik.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Async-Await mintázat használata közös Queue Storage API-kkal

Ez a példa bemutatja, hogyan használható az Async-Await mintázat a közös Queue Storage API-kkal. A minta meghívja az adott módszerek aszinkron verzióját. Az aszinkronitást az egyes módszerek *Async* utótagja jelöli. Ha Async módszert használ, az Async-Await mintázat felfüggeszti a helyi végrehajtást a hívás befejeződéséig. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más feladatokkal foglalkozzon. Ennek segítségével elkerülhetők a szűk keresztmetszetek a teljesítményben, és az alkalmazás általános válaszkészsége is javul. További információk az Async-Await mintázat használatáról .NET-keretrendszerben: [Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Async és Await (C# és Visual Basic)).

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Egyéb lehetőségek az üzenetek eltávolításához az üzenetsorból

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

A következő kódrészlet a [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) metódus használatával 20 üzenetet kap egy hívásban. Ezután az összes üzenetet feldolgozza egy `foreach` hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az összes üzenetet egyszerre 5 percben indítja el, ezért a hívása óta az 5 perc elteltével a `ReceiveMessages` rendszer a nem törölt üzeneteket ismét láthatóvá válik.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

Az alábbi példakód a [GetMessages](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) módszer segítségével egyszerre 20 üzenetet kér le. Ezután az összes üzenetet feldolgozza egy `foreach` hurok használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az összes üzenetet egyszerre 5 percben indítja el, ezért a hívása óta az 5 perc elteltével a `GetMessages` rendszer a nem törölt üzeneteket ismét láthatóvá válik.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Megbecsülheti egy üzenetsorban található üzenetek számát. A [GetProperties](/dotnet/api/azure.storage.queues.queueclient.getproperties) metódus kéri a Queue szolgáltatás, hogy kérje le az üzenetsor tulajdonságait, beleértve az üzenetek darabszámát is. A [ApproximateMessagesCount](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) tulajdonság a várólistában lévő üzenetek hozzávetőleges számát tartalmazza. Ez a szám nem kevesebb, mint a várólistában lévő üzenetek tényleges száma, de magasabb lehet.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

Megbecsülheti egy üzenetsorban található üzenetek számát. A [FetchAttributes](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) módszer lekéri a Queue szolgáltatásból az üzenetsorra vonatkozó attribútumokat, amelyek között megtalálható az üzenetek száma is. A [ApproximateMessageCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) tulajdonság a metódus által lekért utolsó értéket adja vissza a `FetchAttributes` Queue szolgáltatás hívása nélkül.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Üzenetsor törlése

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Egy üzenetsor és az összes benne foglalt üzenet törléséhez hívja meg a [Delete](/dotnet/api/azure.storage.queues.queueclient.delete) módszert az üzenetsor-objektumhoz.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

Egy üzenetsor és az összes benne foglalt üzenet törléséhez hívja meg a [Delete](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) módszert az üzenetsor-objektumhoz.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>További lépések

Most, hogy már megismerte a Queue Storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről is.

- A Queue szolgáltatás elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:
  - [A Storage ügyféloldali kódtára a .NET-hez – referencia](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  - [REST API-referencia](https://msdn.microsoft.com/library/azure/dd179355)
- Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.
  - [Get started with Azure Table Storage using .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) (Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel) a strukturált adatok tárolásához.
  - [Get started with Azure Blob storage using .NET](../blobs/storage-dotnet-how-to-use-blobs.md) (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel) a strukturálatlan adatok tárolásához.
  - [Csatlakozzon az SQL Database adatbázishoz .NET (C#) használatával](../../azure-sql/database/connect-query-dotnet-core.md) a relációs adatok tárolásához.
- Megtudhatja, hogyan egyszerűsítheti az Azure Storage-ban az [Azure WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)használatával írt kódot.
