---
title: Az Azure Queue storage 2.1-es használata a Pythonból – Azure Storage
description: Ismerje meg, hogyan használhatja az Azure Queue service v2.1 python-ból várólisták létrehozásához és törléséhez, valamint üzenetek beszúrásához, leéséhez és törléséhez.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: ca0831fd7554058d21e315b67d6965579af1d38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060916"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Az Azure Queue storage 2.1-es használata pythonból

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Ez a cikk bemutatja a gyakori forgatókönyvek az Azure Queue storage szolgáltatás használatával. A tárgyalt forgatókönyvek közé tartozik a várólista-üzenetek beszúrása, bepillantása, betekintése és törlése, valamint a várólisták létrehozása és törlése.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés

A cikkben szereplő minták Pythonban íródnak, és a [Microsoft Azure Storage SDK for Python t]használja. A várólistákról a [Következő lépések](#next-steps) című szakaszban talál további információt.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>A Pythonhoz készült Azure Storage SDK letöltése és telepítése

Az [Azure Storage SDK python hoz](https://github.com/azure/azure-storage-python) python 2.7-es, 3.3-as vagy újabb verziója.
 
### <a name="install-via-pypi"></a>Telepítés PyPi-n keresztül

A Python package indexen (PyPI) keresztül történő telepítéshez írja be a következőt:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Ha az Azure Storage SDK-ról frissít a Python 0.36-os vagy korábbi `pip uninstall azure-storage` verziójához, távolítsa el a régebbi SDK-t a legújabb csomag telepítése előtt.

Az alternatív telepítési módszerekről az [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/)című témakörben talál.

## <a name="view-the-sample-application"></a>A mintaalkalmazás megtekintése

A Python azure-várólistákkal való használatát bemutató mintaalkalmazás megtekintéséhez és futtatásához lásd: [Azure Storage: Első lépések az Azure-várólistákkal pythonban.](https://github.com/Azure-Samples/storage-queue-python-getting-started) 

A mintaalkalmazás futtatásához győződjön meg `azure-storage-queue` `azure-storage-common` arról, hogy telepítette a és a csomagokat is.

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Az [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) objektum lehetővé teszi a várólistákkal való munkát. A következő kód `QueueService` létrehoz egy objektumot. Adja hozzá a következőket minden olyan Python-fájl tetején, amelyben programozott módon szeretné elérni az Azure Storage-ot:

```python
from azure.storage.queue import QueueService
```

A következő kód `QueueService` létrehoz egy objektumot a tárfiók nevével és a fiókkulcsával. Cserélje ki *a fiókom* és *a kulcs* a fiók nevét és kulcsát.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Üzenet beszúrása egy üzenetsorba

Ha üzenetet szeretne beszúrni egy várólistába, a [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) módszerrel hozzon létre egy új üzenetet, és adja hozzá a várólistához.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Az Azure-várólista-üzenetek szövegként tárolódnak. Ha bináris adatokat szeretne tárolni, a Base64 kódolási és dekódolási függvényeket állítsa be a várólista-szolgáltatás objektumára, mielőtt üzenetet helyezne a várólistába.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Betekintés a következő üzenetbe

A peek_messages [metódus](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) hívásával anélkül tekinthet meg a várólista elején lévő üzenetbe, hogy eltávolítaná a várólistából. Alapértelmezés szerint `peek_messages` egyetlen üzenetbe tekint be.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Üzenetek várólistán stalegének törlése

A kód két lépésben távolítja el az üzenetet a várólistából. Amikor felhívja [get_messages,](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)alapértelmezés szerint a következő üzenet jelenik meg a várólistában. A várólistából `get_messages` érkező üzenetek láthatatlanná válnak a várólistából érkező üzeneteket olvasó bármely más kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívnia [delete_message.](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-) Az üzenet eltávolításának kétlépéses folyamata biztosítja, hogy ha a kód hardver- vagy szoftverhiba miatt nem tudja feldolgozni az üzenetet, a kód egy másik példánya megkapja ugyanazt az üzenetet, és újra próbálkozhat. A kód `delete_message` az üzenet feldolgozása után azonnal hív.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. A következő kód `get_messages` példa a módszer segítségével kap 16 üzenetet egy hívás. Ezután feldolgozza az egyes üzeneteket egy for ciklus használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása

Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód az [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) metódust használja az üzenetek frissítéséhez. A láthatósági időout 0-ra van állítva, ami azt jelenti, hogy az üzenet azonnal megjelenik, és a tartalom frissül.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

Megbecsülheti egy üzenetsorban található üzenetek számát. A [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) metódus arra kéri a várólista-szolgáltatást, `approximate_message_count`hogy adja vissza a várólista metaadatait, a és a . Az eredmény csak hozzávetőleges, mert az üzenetek hozzáadhatók vagy eltávolíthatók, miután a várólista-szolgáltatás válaszol a kérésre.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Üzenetsor törlése

A várólisták és a benne lévő összes üzenet törléséhez hívja meg a [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) metódust.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta a várólista-tárolás alapjait, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg.

* [Az Azure-várólisták Python API-hivatkozása](/python/api/azure-storage-queue)
* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK python-hoz]: https://github.com/Azure/azure-storage-python
