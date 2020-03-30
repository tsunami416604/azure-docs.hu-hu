---
title: Várólista-tároló használata (C++) - Azure Storage
description: Ismerje meg, hogyan használhatja a várólista-tárolási szolgáltatást az Azure-ban. A minták at C++ nyelven írják.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 4fe543010df9514cb2b22c56482a4b592574e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941783"
---
# <a name="how-to-use-queue-storage-from-c"></a>A Queue Storage használata C++-szal
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthatja végre a gyakori forgatókönyveket az Azure Queue storage szolgáltatás használatával. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják. A tárgyalt forgatókönyvek közé tartozik a várólista-üzenetek **beszúrása,** **bepillantása,** **bepillantása**és **törlése,** valamint **várólisták létrehozása és törlése.**

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. Az ajánlott verzió a Storage ügyféloldali kódtárának 2.2.0-s verziója, amely elérhető a [NuGeten](https://www.nuget.org/packages/wastorage) vagy a [GitHubon](https://github.com/Azure/azure-storage-cpp/) keresztül.
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása
Ebben az útmutatóban olyan tárolási funkciókat fog használni, amelyek egy C++ alkalmazáson belül futtathatók.

Ehhez telepítenie kell az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát, és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésben.

Az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát az alábbi módszerekkel telepítheti:

* **Linux:** Kövesse az Azure [Storage Ügyféltár C++ README: Első lépések linuxos](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) lapján megadott utasításokat.
* **Windows:** Windows rendszerben használja a [vcpkg](https://github.com/microsoft/vcpkg) függőségkezelőt. Kövesse a [gyorsindítást](https://github.com/microsoft/vcpkg#quick-start) a vcpkg inicializálásához. Ezután a következő paranccsal telepítse a tárat:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

A README-fájlban útmutatót talál a forráskód létrehozásához és a [NuGet-be](https://github.com/Azure/azure-storage-cpp#download--install) való exportálásához.

## <a name="configure-your-application-to-access-queue-storage"></a>Az alkalmazás konfigurálása a várólista-tároló eléréséhez
Adja hozzá a következő include utasítások a tetején a C ++ fájl, ahol szeretné használni az Azure storage API-k eléréséhez várólisták:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure storage-kapcsolati karakterlánc beállítása
Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ügyfélalkalmazásban való futtatáskor meg kell adnia a tárolási kapcsolat karakterláncát a következő formátumban, a tárfiók nevével és az Azure Portalon az *AccountName* és *accountKey* értékekhez az [Azure Portalon](https://portal.azure.com) felsorolt tárfiók tárolási hozzáférési kulcsával. A tárfiókokról és a hozzáférési kulcsokról az [Azure Storage-fiókok – további](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)információt talál. Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Az alkalmazás helyi Windows-számítógépen való teszteléséhez használhatja az [Azure SDK-val](https://azure.microsoft.com/downloads/)telepített Microsoft [Azure-tárterület-emulátort.](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) A storage-emulátor egy olyan segédprogram, amely szimulálja a blob, várólista és table szolgáltatások elérhető az Azure-ban a helyi fejlesztői gépen. A következő példa bemutatja, hogyan deklarálhat statikus mezőt a helyi Storage Emulatorhoz használható kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure storage emulátor ának elindításához válassza a **Start** gombot, vagy nyomja le a **Windows** billentyűt. Kezdje el beírni az **Azure Storage Emulator ,** és válassza ki a Microsoft Azure **Storage-emulátor** az alkalmazások listájából.

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.

## <a name="retrieve-your-connection-string"></a>A kapcsolati sztring lekérése
A **cloud_storage_account** osztály segítségével képviselheti a tárfiók adatait. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Útmutató: Várólista létrehozása
A **cloud_queue_client** objektum lehetővé teszi a várólisták referenciaobjektumainak bekésését. A következő kód létrehoz egy **cloud_queue_client** objektumot.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

A **cloud_queue_client** objektum segítségével hivatkozást kaphat a használni kívánt várólistára. Létrehozhatja a várólistát, ha nem létezik.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: Üzenet beszúrása várólistába
Ha üzenetet szeretne beszúrni egy meglévő várólistába, először hozzon létre egy új **cloud_queue_message.** Ezután hívja meg a **add_message** metódust. A **cloud_queue_message** karakterláncból vagy **bájttömbből** hozható létre. Az alábbi kód létrehoz egy üzenetsort (ha még nem létezik), és beszúrja a „Hello, World” üzenetet:

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

## <a name="how-to-peek-at-the-next-message"></a>Hogyan: Peek a következő üzenet
A várólista elején lévő üzenetbe anélkül tekinthet meg, hogy eltávolítaná a várólistából a **peek_message** metódus hívásával.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: A várólistán lévő üzenetek tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel n alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

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

## <a name="how-to-de-queue-the-next-message"></a>Útmutató: A következő üzenet várólistájának törlése
A kód két lépésben távolít el egy üzenetet az üzenetsorból. Amikor felhívja **get_message,** a következő üzenet jelenik meg a sorban. A **get_message** visszaküldött üzenet láthatatlanná válik a várólistából érkező üzeneteket olvasó bármely más kód számára. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívnia **delete_message.** Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód az üzenet feldolgozása után **delete_message.**

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

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Útmutató: További lehetőségek kihasználása az üzenetek que-que-jének törléséhez
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. A következő kódpélda a **get_messages** módszerrel 20 üzenetet kap egy hívásban. Ezután feldolgozza az egyes üzeneteket egy **for** ciklus használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Ne feledje, hogy az 5 perc kezdődik az összes üzenetet egy időben, így 5 perc eltelt, mivel a hívás **get_messages**, minden üzenet, amely nem törölték lesz látható újra.

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

## <a name="how-to-get-the-queue-length"></a>Útmutató: A várólista hosszának beszereznie
Megbecsülheti egy üzenetsorban található üzenetek számát. A **download_attributes** metódus kéri a várólista-szolgáltatást a várólista-attribútumok beolvasására, beleértve az üzenetek számát is. A **approximate_message_count** metódus leteszi a várólistában lévő üzenetek hozzávetőleges számát.

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

## <a name="how-to-delete-a-queue"></a>Útmutató: Várólista törlése
Egy várólista és a benne lévő összes üzenet törléséhez hívja meg a **delete_queue_if_exists** metódust a várólista-objektumon.

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
Most, hogy megtanulta a várólista-tárolás alapjait, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg az Azure Storage-ról.

* [A Blob Storage használata C++ oldalról](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [A C++ táblatároló használata](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Azure Storage-erőforrások listázása C++-ban](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [C++ referenciatároló ügyféltár](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/)
