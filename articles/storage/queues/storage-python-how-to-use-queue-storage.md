---
title: A Python a Queue storage használata |} Microsoft Docs
description: 'Útmutató: az Azure Queue szolgáltatás Python segítségével hozza létre, és törli az üzenetsorok, és helyezze, beolvasása, és törli az üzenetet.'
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: cc0d2da2-379a-4b58-a234-8852b4e3d99d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: c7976c01436b1c30880bfd4c57cb97f72a4f48b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
ms.locfileid: "25986749"
---
# <a name="how-to-use-queue-storage-from-python"></a>How to use Queue storage from Python (A Queue Storage használata Pythonnal)
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a szolgáltatást az Azure Queue storage szolgáltatást használó általános forgatókönyvhöz. A mintákat a Python és -felhasználási nyelven íródtak a [Microsoft Azure Storage SDK for Python]. Az ismertetett forgatókönyvek **beszúrása**, **megtekintésekor**, **első**, és **törlése** üzenetek várólistára, valamint **létrehozása és törlése várólisták**. A várólisták további információkért tekintse át a [lépések] részt.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Töltse le és telepítse az Azure Storage SDK Python-hez

Python-hez készült Azure Storage szolgáltatás SDK Python 2.7, 3.3-as, 3.4, 3.5-ös vagy 3.6 igényel, és 4 csomagokban származnak: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` és `azure-storage-queue`. Ebben az oktatóanyagban fogjuk használni `azure-storage-queue` csomag.
 
### <a name="install-via-pypi"></a>PyPi keresztül

A Python-Csomagindexet (PyPI) keresztül telepítéséhez írja be:

```bash
pip install azure-storage-queue
```


> [!NOTE]
> Ha az Azure Storage SDK-ból a Python 0.36 vagy korábbi verzióját frissíti, akkor először eltávolításához használja `pip uninstall azure-storage` , már nem szeptemberben a Storage szolgáltatás SDK Python csomagban.
> 
> 

Alternatív módszerek, látogasson el a [Azure Storage szolgáltatás SDK a Githubon Python](https://github.com/Azure/azure-storage-python/).

## <a name="how-to-create-a-queue"></a>Útmutató: A várólista létrehozása
A **QueueService** objektum lehetővé teszi, hogy az üzenetsorok. Az alábbi kód létrehoz egy **QueueService** objektum. Adja hozzá a következő tetejénél található minden olyan Python fájlt, amelyben programon keresztüli eléréséhez az Azure Storage kívánja:

```python
from azure.storage.queue import QueueService
```

Az alábbi kód létrehoz egy **QueueService** objektumba a tárfiók nevét és a fiók kulcsot. "Myaccount" és "SajátKulcs" cserélje le a fióknevet és kulcsot.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Útmutató: A várólista üzenet beszúrása
Üzenet beszúrása egy üzenetsort, használja a **put\_üzenet** módszert, hozzon létre egy új üzenetet, és vegye fel a várólistára.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Útmutató: A következő üzenet megtekintése
Is bepillanthat, hogy egy sor elején található üzenetbe anélkül, hogy eltávolítaná az üzenetsorból meghívásával a **betekintés\_üzenetek** metódust. Alapértelmezés szerint **betekintés\_üzenetek** lekéri egyetlen üzenetben.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Útmutató: Created üzenetek
A kód eltávolítja az üzenetet az üzenetsorból két lépést. A hívás esetén **beolvasása\_üzenetek**, a hibaüzenet a következő várólista alapértelmezés szerint. Az üzenet **beolvasása\_üzenetek** ebből a várólistából üzeneteket olvasó többi kód láthatatlanná válik. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Szeretné távolítani az üzenetet az üzenetsorból, meg kell is hívni **törlése\_üzenet**. A kétlépéses folyamat eltávolításával előállított üzenet biztosítja, hogy a kód nem tudja feldolgozni egy üzenetet, hardver- vagy szoftverhiba miatt, a kód egy másik példánya is megkaphassa ugyanazt az üzenetet, és próbálkozzon újra. A kód hívások **törlése\_üzenet** jobb gombbal az üzenet feldolgozása után.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.
Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. Az alábbi példakód a **beolvasása\_üzenetek** módszer segítségével 16 üzenetek egy hívásban. Ezután minden üzenetet használatával feldolgozza a hurok. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Útmutató: Az aszinkron üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi által használt kódot a **frissítése\_üzenet** üzenet frissítési módjának. A láthatósági időkorlátot értéke 0, ami azt jelenti, az üzenet jelenik meg azonnal, és a tartalom frissítésekor.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Útmutató: Az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **beolvasása\_várólista\_metaadatok** módszert kéri a queue szolgáltatás az üzenetsorral kapcsolatos metaadatok visszaadását és a **approximate_message_count**. Az eredmény csak azért hozzávetőleges, mert üzenetek hozzáadására vagy törlődik, miután a queue szolgáltatás válaszol a kérésre.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Útmutató: A várólista törlése
Egy üzenetsor és a benne tárolt összes üzenet törléséhez hívja meg a **törlése\_várólista** metódust.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Queue storage alapjait, az alábbi hivatkozásokból további.

* [Python fejlesztői központ](/develop/python/)
* [Az Azure Storage-szolgáltatások REST API-ja](http://msdn.microsoft.com/library/azure/dd179355)
* [Az Azure Storage csapat blogja]
* [Microsoft Azure Storage SDK for Python]

[Az Azure Storage csapat blogja]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python