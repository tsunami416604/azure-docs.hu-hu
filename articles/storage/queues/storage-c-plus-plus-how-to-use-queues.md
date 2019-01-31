---
title: A queue storage (C++) használata |} A Microsoft Docs
description: Ismerje meg, a queue storage szolgáltatás használata az Azure-ban. A minták C++ nyelven íródtak.
services: storage
author: cbrooksmsft
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 05/11/2017
ms.author: cbrooksmsft
ms.subservice: queues
ms.openlocfilehash: 1f2f52fc08ab4da4a7525f3018b7a9aea2f7c576
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457361"
---
# <a name="how-to-use-queue-storage-from-c"></a>A c++ segítségével a Queue Storage használata
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre az Azure Queue storage szolgáltatást használó általános forgatókönyvhöz. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](http://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják. Az ismertetett forgatókönyvek között megtalálható **beszúrása**, **Bepillantás**, **első**, és **törlése** várólista-üzenetek, valamint  **létrehozása és törlése a várólisták**.

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. Az ajánlott verzió a Storage ügyféloldali kódtárának 2.2.0-s verziója, amely elérhető a [NuGeten](http://www.nuget.org/packages/wastorage) vagy a [GitHubon](http://github.com/Azure/azure-storage-cpp/) keresztül.
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása
Ebben az útmutatóban tárolási szolgáltatásokkal, amely egy C++-alkalmazásból futtathat fogja használni.

Ehhez telepítenie kell az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát, és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésben.

Az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát az alábbi módszerekkel telepítheti:

* **Linux:** Kövesse az utasításokat adott a [Azure Storage ügyféloldali kódtára a C++ információs](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) lapot.
* **Windows:** A Visual Studióban válassza a **Tools (Eszközök) > NuGet Package Manager (NuGet-csomagkezelő) > Package Manager Console (Csomagkezelő konzol)** elemet. Írja be a következő parancsot a [NuGet Package Manager console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) nyomja le az ENTER **ENTER**.

```  
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>A Queue Storage eléréséhez az alkalmazás konfigurálása
Adja hozzá a következőket tartalmazzák-utasítások használatával kívánja az Azure storage API-k várólisták eléréséhez használja a C++-fájl elejéhez:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ha egy ügyfélalkalmazás fut, meg kell adnia a tárolási kapcsolati karakterlánc a következő formátumban szerepel a tárfiók nevét a tárfiók és a tárelérési kulcs használatával a [az Azure Portal](https://portal.azure.com) számára a *AccountName* és *AccountKey* értékeket. Információk a storage-fiókok és a hozzáférési kulcsok: [kapcsolatos Azure-Tárfiókok](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Az alkalmazás tesztelése a helyi Windows-számítógép, használhatja a Microsoft Azure [storage emulator](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) együtt települ, amely a [Azure SDK](https://azure.microsoft.com/downloads/). A storage emulator egy segédprogram, amely szimulálja a Blob, Queue és Table szolgáltatások, a helyi fejlesztői gépen elérhető az Azure-ban. A következő példa bemutatja, hogyan deklarálhat statikus mezőt a helyi Storage Emulatorhoz használható kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure storage emulator indításához válassza ki a **Start** gombra vagy nyomja le az **Windows** kulcs. Kezdje el beírni **Azure Storage Emulator**, és válassza ki **a Microsoft Azure Storage Emulator** az alkalmazások listájából.

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.

## <a name="retrieve-your-connection-string"></a>A kapcsolati sztring lekérése
Használhatja a **cloud_storage_account** osztály, amely a Storage-fiók adatait jelöli. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Útmutató: Üzenetsor létrehozása
A **cloud_queue_client** objektum lehetővé teszi a várólisták hivatkozási objektumok beolvasása. Az alábbi kód létrehoz egy **cloud_queue_client** objektum.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Használja a **cloud_queue_client** beolvasása egy hivatkozást a használni kívánt várólista-objektum. A várólista hozhat létre, ha még nem létezik.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: Üzenet beszúrása egy üzenetsorba
Üzenet beszúrása egy létező üzenetsorba, először hozzon létre egy új **cloud_queue_message**. Ezután hívja a **add_message** metódust. A **cloud_queue_message** hozható létre vagy egy karakterlánc vagy egy **bájt** tömb. Az alábbi kód létrehoz egy üzenetsort (ha még nem létezik), és beszúrja a „Hello, World” üzenetet:

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
Anélkül, hogy eltávolítaná az üzenetsorból meghívásával is bepillanthat egy üzenetsorban található üzenet a **peek_message** metódust.

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
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. Általában nyilvántartja az újrapróbálkozások számát is, és az üzenetet a rendszer több mint n-szer, ha meg szeretné törlése. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

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
A kód két lépésben távolít el egy üzenetet az üzenetsorból. Meghívásakor **get_message**, a következő üzenetet kap egy üzenetsorban. Lekért üzenet **get_message** válik az adott üzenetsorban üzeneteket olvasó többi kód számára. Befejeződik, az üzenet eltávolítása az üzenetsorból, meg kell is meghívhat **delete_message**. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód meghívja **delete_message** jobb gombbal az üzenet feldolgozása után.

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
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. Az alábbi példakód a **get_messages** metódus lekéréséhez 20 üzenetet egyetlen hívásával. Ezután minden üzenetet használatával feldolgozza a **a** ciklus. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az 5 perc minden elindul, egy időben, ezért után 5 perccel átadott hívása **get_messages**, nem törölt üzenetek újra láthatóvá válnak.

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
Megbecsülheti egy üzenetsorban található üzenetek számát. A **download_attributes** módszert kéri a Queue szolgáltatás lekérdezni az attribútumokat, az üzenetek száma. A **approximate_message_count** metódus lekéri az üzenetek hozzávetőleges száma az üzenetsorban.

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
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja a **delete_queue_if_exists** metódust a várólista-objektum.

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
Most, hogy megismerte a Queue storage alapjait, az alábbi hivatkozásokból tudhat meg többet az Azure Storage.

* [A c++ segítségével Blob Storage használata](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Hogyan használható a Table Storage, a c++ segítségével](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [A C++ Azure Storage-erőforrások listája](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [A Storage ügyféloldali kódtára a c++ nyelvhez – dokumentáció](http://azure.github.io/azure-storage-cpp)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
