---
title: A Queue storage, a Python használatával |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre, és törli az üzenetsorok, az Azure Queue szolgáltatás, a Python használatához és helyezze, beolvasása, és törli az üzenetet.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: tamram
ms.component: queues
ms.openlocfilehash: 0edb90ca7324d47beaa5133d423928e615ff33a9
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742813"
---
# <a name="how-to-use-queue-storage-from-python"></a>How to use Queue storage from Python (A Queue Storage használata Pythonnal)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre az Azure Queue storage szolgáltatást használó általános forgatókönyvhöz. A minták a Python és -felhasználási nyelven írták az [A Microsoft Azure Storage-hoz készült Python SDK]. Az ismertetett forgatókönyvek között megtalálható **beszúrása**, **Bepillantás**, **első**, és **törlése** várólista-üzenetek, valamint  **létrehozása és törlése a várólisták**. Üzenetsorok további információkért tekintse meg az [a következő lépések] szakaszban.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Töltse le és telepítse az Azure Storage SDK a Pythonhoz

A [Pythonhoz készült Azure Storage SDK](https://github.com/azure/azure-storage-python) van szükség a Python 2.7-es, 3.3-as, 3.4-es, 3.5-ös és 3.6-os.
 
### <a name="install-via-pypi"></a>Keresztül PyPi telepítése

Telepítse a Python-Csomagindexet (PyPI) keresztül, írja be:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Ha 0.36 vagy korábbi verzióját a Pythonhoz készült Azure Storage SDK frissíti, távolítsa el a régebbi SDK használatával `pip uninstall azure-storage` a legújabb csomag telepítése előtt.

Alternatív telepítési módjaival kapcsolatban lásd: [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>A mintaalkalmazás megtekintése

Megtekintheti és futtathat egy mintaalkalmazást, amely bemutatja, hogyan használhatja a Pythont az Azure-üzenetsorok: [Azure Storage: Ismerkedés az Azure-üzenetsorok a Pythonban](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Futtassa a mintaalkalmazást, győződjön meg arról, hogy mindkettő telepítése a `azure-storage-queue` és `azure-storage-common` csomagokat.

## <a name="how-to-create-a-queue"></a>kézikönyv: Várólista létrehozása

A **QueueService** objektum lehetővé teszi a várólisták használata. Az alábbi kód létrehoz egy **QueueService** objektum. Adja hozzá a következő, bármely Python-fájlt, amelyben a programozott módon érheti el az Azure Storage kívánt tetején:

```python
from azure.storage.queue import QueueService
```

Az alábbi kód létrehoz egy **QueueService** objektumba a tárfiók nevét és kulcsát. Cserélje le a "myaccount" és "mykey" a fiók nevére és kulcsára.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>kézikönyv: Üzenet beszúrása egy üzenetsorba
Üzenet beszúrása egy üzenetsorba, használja a **put\_üzenet** metódust hozzon létre egy új üzenetet, és adja hozzá az üzenetsorhoz.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>kézikönyv: Betekintés a következő üzenet
Anélkül, hogy eltávolítaná az üzenetsorból meghívásával is bepillanthat egy üzenetsorban található üzenet a **betekintési\_üzenetek** metódust. Alapértelmezés szerint **betekintési\_üzenetek** lekéri egy adott üzenet.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>kézikönyv: Üzenet eltávolítása a sorból
A kód két lépésben egy üzenetsorból eltávolítja az üzenetet. Meghívásakor **első\_üzenetek**, a következő üzenetet kap egy üzenetsorban lévő alapértelmezés szerint. Lekért üzenet **első\_üzenetek** válik az adott üzenetsorban üzeneteket olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Befejeződik, az üzenet eltávolítása az üzenetsorból, meg kell is meghívhat **törlése\_üzenet**. Az üzenet eltávolítása a két lépésből álló folyamat biztosítja, hogy a kód nem tudja feldolgozni egy üzenetet, mert hardveres vagy szoftveres hiba, amikor a kód egy másik példánya is megkaphassa ugyanazt az üzenetet, és próbálkozzon újra. A kód meghívja **törlése\_üzenet** jobb gombbal az üzenet feldolgozása után.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.
Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. Az alábbi példakód a **első\_üzenetek** metódus egyetlen hívásával 16 darab lekéréséhez. Ezután feldolgozza, hogy minden üzenet használatával egy for ciklusba. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>kézikönyv: Üzenetsorban található üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kódot a **frissítése\_üzenet** metódus üzenet frissítéséhez. A láthatósági időkorlátot értéke 0, ami azt jelenti, az üzenet jelenik meg azonnal, és a tartalom frissítésekor.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>kézikönyv: Az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **első\_várólista\_metaadatok** módszert kéri az üzenetsorral kapcsolatos metaadatokat visszaadni a queue szolgáltatás és a **approximate_message_count**. Ez csak hozzávetőleges mert üzeneteket is lehet hozzáadni vagy eltávolítani után a queue szolgáltatás válaszol a kérésre.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>kézikönyv: Üzenetsor törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja a **törlése\_várólista** metódust.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Queue storage alapjait, kövesse az alábbi hivatkozások további.

* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[A Microsoft Azure Storage-hoz készült Python SDK]: https://github.com/Azure/azure-storage-python
