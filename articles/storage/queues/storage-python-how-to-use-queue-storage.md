---
title: Az Azure üzenetsor Storage 2.1-es verziójának használata a Pythonból – Azure Storage
description: Megtudhatja, hogyan hozhat létre és törölhet várólistákat az Azure Queue szolgáltatás 2.1-es verzióról a Pythonból, és hogyan szúrhat be, kérhet le és törölhet üzeneteket.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-october2019, tracking-python
ms.openlocfilehash: 46d144a95708ac834478871ca27763f0ebd3b201
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84805239"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Az Azure üzenetsor-tároló v 2.1 használata a Pythonból

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Ez a cikk az Azure üzenetsor-tárolási szolgáltatás használatával kapcsolatos gyakori forgatókönyveket mutatja be. A tárgyalt forgatókönyvek közé tartozik a várólista-üzenetek beszúrása, bepillantása, beolvasása és törlése, valamint a várólisták létrehozása és törlése.

> [!IMPORTANT]
> Ez a cikk a Pythonhoz készült Azure Storage ügyféloldali kódtár örökölt verziójára vonatkozik. A legújabb verzió használatának megkezdéséhez tekintse meg a gyors útmutató [: Azure üzenetsor Storage ügyféloldali kódtára a Pythonhoz](storage-quickstart-queues-python.md) című témakört.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés

A cikkben szereplő minták Pythonban íródtak, és a [Pythonhoz készült Microsoft Azure Storage SDK]-t használják. A várólistákkal kapcsolatos további információkért tekintse meg a [következő lépések](#next-steps) szakaszt.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>A Pythonhoz készült Azure Storage SDK letöltése és telepítése

A [Pythonhoz készült Azure Storage SDK](https://github.com/azure/azure-storage-python) használatához a Python 2,7-es, 3,3-as vagy újabb verziója szükséges.
 
### <a name="install-via-pypi"></a>Telepítés a PyPi-on keresztül

A Python Package index (PyPI) használatával történő telepítéshez írja be a következőt:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Ha a Python 0,36-es vagy korábbi verziójú Azure Storage SDK-ból frissít, távolítsa el a régebbi SDK-t a `pip uninstall azure-storage` legújabb csomag telepítése előtt.

Alternatív telepítési módszerek: [a Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>A minta alkalmazás megtekintése

A Python és az Azure Queues használatának módját bemutató minta alkalmazás megtekintéséhez és futtatásához tekintse meg az [Azure Storage: első lépések a Python Azure Queues](https://github.com/Azure-Samples/storage-queue-python-getting-started)használatával című témakört. 

A minta alkalmazás futtatásához győződjön meg arról, hogy a és a csomagokat is telepítette `azure-storage-queue` `azure-storage-common` .

## <a name="create-a-queue"></a>Üzenetsor létrehozása

A [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) objektum lehetővé teszi a várólistákkal való munkavégzést. A következő kód létrehoz egy `QueueService` objektumot. Adja hozzá a következőt az összes olyan Python-fájlhoz, amelyben programozottan szeretné elérni az Azure Storage-t:

```python
from azure.storage.queue import QueueService
```

A következő kód létrehoz egy `QueueService` objektumot a Storage-fiók neve és a fiók kulcsa alapján. Cserélje le a *MyAccount* és a *Mykey* nevet a fiók nevére és kulcsára.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Üzenet beszúrása egy üzenetsorba

Egy üzenet üzenetsorbe való beszúrásához a [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) metódussal hozzon létre egy új üzenetet, és vegye fel azt a várólistába.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Az Azure üzenetsor-üzenetei szövegként vannak tárolva. Ha bináris adatok tárolására van szüksége, állítson be Base64 kódolást és dekódolási függvényeket a várólista-szolgáltatási objektumon, mielőtt üzenetet hozna a várólistán.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Betekintés a következő üzenetbe

A várólista elején lévő üzenetbe való betekintés nélkül is betekintést nyerhet a várólistából a [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) metódus meghívásával. Alapértelmezés szerint `peek_messages` a betekintés egyetlen üzenetbe kerül.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Üzenetek várólistára helyezése

A kód két lépésben eltávolít egy üzenetet egy várólistából. [Get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)hívásakor a rendszer alapértelmezés szerint a következő üzenetet jeleníti meg egy várólistában. A visszaadott üzenet a `get_messages` várólistából beolvasott más kódokba is láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Az üzenet várólistából való eltávolításának befejezéséhez meg kell hívnia a [delete_messaget](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-)is. Az üzenetek eltávolításának kétlépéses folyamata biztosítja, hogy ha a kód a hardver vagy a szoftver meghibásodása miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya ugyanazt az üzenetet kapja, és próbálkozzon újra. A kód meghívása `delete_message` közvetlenül az üzenet feldolgozása után történik.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. A következő kódrészlet a `get_messages` metódus használatával 16 üzenetet kap egy hívásban. Ezután feldolgozza az egyes üzeneteket a for loop használatával. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása

Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód a [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) metódust használja egy üzenet frissítéséhez. A láthatósági időkorlát 0 értékre van állítva, ami azt jelenti, hogy az üzenet azonnal megjelenik, és a tartalom frissül.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

Megbecsülheti egy üzenetsorban található üzenetek számát. A [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) metódus arra kéri a várólista-szolgáltatást, hogy a várólista metaadatainak visszaadása és a `approximate_message_count` . Az eredmény csak hozzávetőleges, mert az üzenetsor-szolgáltatás a kérésre való reagálás után üzeneteket adhat hozzá vagy távolíthat el.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Üzenetsor törlése

Ha törölni szeretne egy várólistát és a benne található összes üzenetet, hívja meg a [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) metódust.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a várólista-tárolás alapjait, kövesse az alábbi hivatkozásokat további információért.

* [Az Azure Queues Python API-referenciája](/python/api/azure-storage-queue)
* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[A Pythonhoz készült Microsoft Azure Storage SDK]: https://github.com/Azure/azure-storage-python
