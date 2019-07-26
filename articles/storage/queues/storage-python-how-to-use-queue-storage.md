---
title: A üzenetsor-tároló használata a Pythonból – Azure Storage
description: Megtudhatja, hogyan hozhat létre és törölhet várólistákat az Azure Queue szolgáltatás Python használatával, valamint hogyan szúrhat be, kérhet le és törölhet üzeneteket.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 141999f4119ac92e2b8846477c50edf8fba027d0
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360022"
---
# <a name="how-to-use-queue-storage-from-python"></a>How to use Queue storage from Python (A Queue Storage használata Pythonnal)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure üzenetsor-tárolási szolgáltatás használatával. A minták Pythonban íródnak, és a Pythonhoz készült [A Pythonhoz készült Microsoft Azure Storage SDK]-t használják. A tárgyalt forgatókönyvek közé tartozik például a várólista-üzenetek **beszúrása**, bepillantása, beolvasása és **törlése** , valamint a  **várólisták létrehozása és törlése**. A várólistákkal kapcsolatos további információkért tekintse meg a [következő lépések] szakaszt.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Töltse le és telepítse a Pythonhoz készült Azure Storage SDK-t

A [Pythonhoz készült Azure Storage SDK](https://github.com/azure/azure-storage-python) használatához Python 2,7, 3,3, 3,4, 3,5 vagy 3,6 szükséges.
 
### <a name="install-via-pypi"></a>Telepítés a PyPi-on keresztül

A Python Package index (PyPI) használatával történő telepítéshez írja be a következőt:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Ha a Python 0,36-es vagy korábbi verziójú Azure Storage SDK-ból frissít, távolítsa el a régebbi `pip uninstall azure-storage` SDK-t a legújabb csomag telepítése előtt.

Alternatív telepítési módszerek: [a Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>A minta alkalmazás megtekintése

A Python és az Azure Queues használatának módját bemutató minta alkalmazás megtekintéséhez és futtatásához tekintse [meg az Azure Storage: Első lépések a Python](https://github.com/Azure-Samples/storage-queue-python-getting-started)Azure Queues szolgáltatásával. 

A minta alkalmazás futtatásához győződjön meg arról, hogy a és `azure-storage-queue` `azure-storage-common` a csomagokat is telepítette.

## <a name="how-to-create-a-queue"></a>kézikönyv: Várólista létrehozása

A **QueueService** objektum lehetővé teszi a várólistákkal való munkavégzést. A következő kód létrehoz egy **QueueService** objektumot. Adja hozzá a következőt az összes olyan Python-fájlhoz, amelyben programozottan szeretné elérni az Azure Storage-t:

```python
from azure.storage.queue import QueueService
```

A következő kód létrehoz egy **QueueService** objektumot a Storage-fiók neve és a fiók kulcsa alapján. Cserélje le a "MyAccount" és a "Mykey" nevet a fiókjának nevére és kulcsára.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>kézikönyv: Üzenet beszúrása egy várólistába
Ha üzenetet szeretne beszúrni egy várólistába, az **\_üzenet** beszúrása metódussal hozzon létre egy új üzenetet, és vegye fel azt a várólistába.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>kézikönyv: Betekintés a következő üzenetbe
A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistába. **\_** Alapértelmezés szerint a **betekintés\_üzenetei** egyetlen üzenetbe vannak bepillantva.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>kézikönyv: Üzenetek várólistára helyezése
A kód két lépésben eltávolít egy üzenetet egy várólistából. A **Get\_üzenetek**hívásakor a következő üzenet jelenik meg egy várólistában, alapértelmezés szerint. A lekérési **\_üzenetekből** visszaadott üzenet láthatatlanná válik a várólistáról érkező más kódok üzeneteiben. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívnia **a\_törlési üzenetet**is. Az üzenetek eltávolításának kétlépéses folyamata biztosítja, hogy ha a kód a hardver vagy a szoftver meghibásodása miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya ugyanazt az üzenetet kapja, és próbálkozzon újra. A kód meghívja a **delete\_üzenetet** közvetlenül az üzenet feldolgozását követően.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.
Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. A következő kódrészlet az **üzenetek\_** lekérése módszert használja a 16 üzenet egyetlen hívásban való lekéréséhez. Ezután feldolgozza az egyes üzeneteket a for loop használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>kézikönyv: Várólistán lévő üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód az üzenet **frissítése\_** módszert használja az üzenet frissítéséhez. A láthatósági időkorlát 0 értékre van állítva, ami azt jelenti, hogy az üzenet azonnal megjelenik, és a tartalom frissül.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>kézikönyv: Várólista hosszának beolvasása
Megbecsülheti egy üzenetsorban található üzenetek számát. A **\_várólista\_-metaadatok** beolvasása módszer azt kéri, hogy a várólista szolgáltatás metaadatokat ad vissza a várólistáról és a **approximate_message_count**. Az eredmény csak hozzávetőleges, mert az üzenetsor-szolgáltatás a kérésre való reagálás után üzeneteket adhat hozzá vagy távolíthat el.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>kézikönyv: Üzenetsor törlése
Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a **delete\_üzenetsor** metódusát.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a várólista-tárolás alapjait, kövesse az alábbi hivatkozásokat további információért.

* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[A Pythonhoz készült Microsoft Azure Storage SDK]: https://github.com/Azure/azure-storage-python
