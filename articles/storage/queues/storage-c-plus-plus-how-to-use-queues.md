---
title: A várólista-tároló (C++) használata – Azure Storage
description: Ismerje meg, hogyan használható a üzenetsor-tárolási szolgáltatás az Azure-ban. A C++minták beírása folyamatban van.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: e268e30e8f8c512dd6efb5a50da45f173e526b62
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721643"
---
# <a name="how-to-use-queue-storage-from-c"></a>A Queue Storage használataC++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure üzenetsor-tárolási szolgáltatás használatával. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják. A tárgyalt forgatókönyvek közétartozik például a várólista-üzenetek **beszúrása**, bepillantása, beolvasása és **törlése** , valamint a **várólisták létrehozása és törlése**.

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. Az ajánlott verzió a Storage ügyféloldali kódtárának 2.2.0-s verziója, amely elérhető a [NuGeten](https://www.nuget.org/packages/wastorage) vagy a [GitHubon](https://github.com/Azure/azure-storage-cpp/) keresztül.
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása
Ebben az útmutatóban a tárolási funkciókat fogja használni, amelyek egy C++ alkalmazáson belül futtathatók.

Ehhez telepítenie kell az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát, és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésben.

Az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát az alábbi módszerekkel telepítheti:

* **Linux** Kövesse az [Azure Storage ügyféloldali C++ kódtár információs](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) oldalának utasításait.
* **Windows:** A Visual Studióban válassza a **Tools (Eszközök) > NuGet Package Manager (NuGet-csomagkezelő) > Package Manager Console (Csomagkezelő konzol)** elemet. Írja be a következő parancsot a [NuGet Package Manager konzolba](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) , majd nyomja le az **ENTER**billentyűt.

```powershell
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása Queue Storage eléréséhez
Adja hozzá a következő include utasításokat a C++ fájl elejéhez, ahol az Azure Storage API-kat szeretné használni a várólisták eléréséhez:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage-beli kapcsolatok karakterláncának beállítása
Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ügyfélalkalmazás esetén a tárolási kapcsolati karakterláncot a következő formátumban kell megadnia, a Storage-fiók neve és az [Azure Portalon](https://portal.azure.com) a *accountname* tartozó Storage-fiók elérési kulcsa alapján. és *AccountKey* értékeket. További információ a Storage-fiókokról és a hozzáférési kulcsokról: [Tudnivalók az Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)-fiókokról. Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Az alkalmazás helyi Windows-számítógépen való teszteléséhez használhatja az [Azure SDK](https://azure.microsoft.com/downloads/)-val telepített Microsoft Azure [Storage emulatort](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) . A Storage Emulator egy olyan segédprogram, amely szimulálja az Azure-ban elérhető blob, üzenetsor és Table szolgáltatásokat a helyi fejlesztési gépen. A következő példa bemutatja, hogyan deklarálhat statikus mezőt a helyi Storage Emulatorhoz használható kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure Storage Emulator elindításához kattintson a **Start** gombra, vagy nyomja le a **Windows** billentyűt. Kezdje el beírni az **Azure Storage emulatort**, és válassza a **Microsoft Azure Storage Emulator** lehetőséget az alkalmazások listájából.

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.

## <a name="retrieve-your-connection-string"></a>A kapcsolati sztring lekérése
A **cloud_storage_account** osztály használatával a Storage-fiók adatait jelenítheti meg. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Útmutató: Üzenetsor létrehozása
A **cloud_queue_client** objektum lehetővé teszi a várólisták hivatkozási objektumainak lekérését. A következő kód létrehoz egy **cloud_queue_client** objektumot.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

A **cloud_queue_client** objektum használatával kérjen egy hivatkozást a használni kívánt várólistára. Ha nem létezik, létrehozhat egy várólistát.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: Üzenet beszúrása egy üzenetsorba
Ha egy üzenetet egy meglévő várólistába szeretne beszúrni, először hozzon létre egy új **cloud_queue_message**. Ezután hívja meg a **add_message** metódust. **Cloud_queue_message** hozható létre egy sztringből vagy egy **bájtos** tömbből. Az alábbi kód létrehoz egy üzenetsort (ha még nem létezik), és beszúrja a „Hello, World” üzenetet:

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: Betekintés a következő üzenetbe
A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistából a **peek_message** metódus meghívásával.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: Üzenetsorban található üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. Általában az újrapróbálkozások száma is megmarad, és ha az üzenet többször is többször próbálkozik, akkor törölni kellene. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>Útmutató: A következő üzenet kivétele az üzenetsorból
A kód két lépésben távolít el egy üzenetet az üzenetsorból. A **get_message**meghívásakor a következő üzenet jelenik meg egy várólistában. A **get_message** által visszaadott üzenet láthatatlanná válik minden más, a várólistáról olvasó üzenetben. Az üzenet várólistából való eltávolításának befejezéséhez a **delete_message**is hívnia kell. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód a **delete_message** közvetlenül az üzenet feldolgozása után hívja meg.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Útmutató: Egyéb lehetőségek az üzenetek eltávolításához az üzenetsorból
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. A következő kódrészlet a **get_messages** metódus használatával 20 üzenetet kap egy hívásban. Ezután feldolgozza az egyes üzeneteket a **for** loop használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az összes üzenet 5 perce egyszerre indul el, ezért 5 perc elteltével a **get_messages**hívása óta a nem törölt üzenetek ismét láthatóvá válnak.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Útmutató: Az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **download_attributes** metódus kéri a Queue szolgáltatás az üzenetsor-attribútumok beolvasására, beleértve az üzenetek darabszámát is. A **approximate_message_count** metódus lekéri az üzenetek hozzávetőleges számát a várólistában.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Útmutató: Üzenetsor törlése
Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a **delete_queue_if_exists** metódust a várólista-objektumon.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a várólista-tárolás alapjait, kövesse az alábbi hivatkozásokat, és ismerkedjen meg az Azure Storage szolgáltatással.

* [A Blob Storage használataC++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [A Table Storage használataC++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Azure Storage-erőforrások listázása itt:C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [A C++ Storage ügyféloldali kódtár referenciája](https://azure.github.io/azure-storage-cpp)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
