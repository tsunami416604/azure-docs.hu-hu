---
title: 'Rövid útmutató: Azure Queue storage-tár v12 - Python'
description: Ismerje meg, hogyan használhatja az Azure Queue Python v12-es kódtár segítségével egy várólista létrehozásához és üzenetek hozzáadásához a várólistához. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket a várólistából. Azt is megtudhatja, hogyan törölhet idát várólistát.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: d6ccd3cc61f9d8244874823be76496a4f4e1073c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199767"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Rövid útmutató: Azure Queue storage ügyfélkódtár v12 Pythonhoz

Első lépések az Azure Queue storage ügyféltár 12-es python-verziójával. Az Azure Queue storage egy olyan szolgáltatás, amely nagy számú üzenetet tárol későbbi lekéréshez és feldolgozáshoz. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját.

Használja az Azure Queue storage ügyfélkódtár v12 python a következőkhöz:

* Üzenetsor létrehozása
* Üzenetek hozzáadása várólistához
* Betekintés a várólistában lévő üzenetekbe
* Üzenet frissítése a várólistában
* Üzenetek fogadása várólistából
* Üzenetek törlése várólistából
* Üzenetsor törlése

[API-referenciadokumentáció–](https://docs.microsoft.com/python/api/azure-storage-queue/index) | [könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue) | [csomagja (Python-csomagindex)](https://pypi.org/project/azure-storage-queue/) | [mintái](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure storage-fiók – [tárfiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) az operációs rendszerhez - 2.7, 3.5 vagy újabb

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti a projekt előkészítése az Azure Queue storage ügyfélkódtár v12 python.This section walks you for preparing a project to work with the Azure Queue storage client library v12 for Python.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy Python-alkalmazás nevű *várósorok-quickstart-v12.*

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Váltson át az újonnan létrehozott *várólisták-quickstart-v12* könyvtárra.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>A csomag telepítése

Telepítse az Azure Blob storage-ügyfélkódtár `pip install` python-csomag a parancs használatával.

```console
pip install azure-storage-queue
```

Ez a parancs telepíti az Azure Queue storage ügyfélkódtár python csomag és az összes olyan kódtár, amelytől függ. Ebben az esetben ez csak az Azure core library for Python.

### <a name="set-up-the-app-framework"></a>Az alkalmazáskeretrendszer beállítása

1. Új szövegfájl megnyitása a kódszerkesztőben
1. Utasítások `import` hozzáadása
1. A program struktúrájának létrehozása, beleértve a legalapvetőbb kivételkezelést

    Itt a kód:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Mentse az új fájlt *queues-quickstart-v12.py* a *várólista-quickstart-v12* könyvtárba.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. A várólista-üzenet mérete legfeljebb 64 KB lehet. Egy várólista több millió üzenetet tartalmazhat, a tárfiók teljes kapacitáskorlátjáig. A várólistákat gyakran használják az aszinkron feldolgozáshoz használt munka hátralékának létrehozására. A várólista-tárolás háromféle erőforrást kínál:

* A tárfiók
* Várólista a tárfiókban
* Üzenetek a várólistán belül

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Várólista tárolási architektúrájának diagramja](./media/storage-queues-introduction/queue1.png)

A következő Python-osztályok segítségével kezelheti ezeket az erőforrásokat:

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` A lehetővé teszi a tárfiók összes várólistájának kezelését.
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` Az osztály lehetővé teszi egy adott várólista és üzenetének kezelését és kezelését.
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` Az osztály a várólistán [lévő receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) hívásakor visszaadott egyes objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek bemutatják, hogyan teheti meg a következő műveleteket az Azure Queue storage-ügyféltár a Pythonhoz:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Üzenetsor létrehozása](#create-a-queue)
* [Üzenetek hozzáadása várólistához](#add-messages-to-a-queue)
* [Betekintés a várólistában lévő üzenetekbe](#peek-at-messages-in-a-queue)
* [Üzenet frissítése a várólistában](#update-a-message-in-a-queue)
* [Üzenetek fogadása várólistából](#receive-messages-from-a-queue)
* [Üzenetek törlése várólistából](#delete-messages-from-a-queue)
* [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati karakterláncát. A kapcsolati karakterlánc a [tárolási kapcsolati karakterlánc konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változót tárolja.

Adja hozzá ezt `try` a kódot a blokkon belül:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Üzenetsor létrehozása

Döntse el az új várólista nevét. Az alábbi kód hozzáfűz egy UUID értéket a várólista nevéhez annak érdekében, hogy az egyedi legyen.

> [!IMPORTANT]
> A várólistanevek csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődniük. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3 és 63 karakter közötti nek is kell lennie. A várólisták elnevezéséről további információt az [Elnevezési várólisták és a metaadatok](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)című témakörben talál.

Hozzon létre egy példányt a [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) osztályból. Ezután hívja meg a [create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) metódust a tárfiókban lévő várólista létrehozásához.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása várólistához

A következő kódrészlet a [send_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) metódus hívásával üzeneteket ad a várólistához. Menti a harmadik `send_message` hívásból visszaadott [QueueMessage-et](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) is. A `saved_message` a program későbbi részében az üzenet tartalmának frissítésére szolgál.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Betekintés a várólistában lévő üzenetekbe

A [peek_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) metódus hívásával tekintsünk be a várólistában lévő üzenetekbe. A `peek_messages` metódus egy vagy több üzenetet olvas le a várólista elejéről, de nem változtatja meg az üzenet láthatóságát.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Üzenet frissítése a várólistában

Az üzenetek tartalmának frissítése a [update_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metódus hívásával. A `update_message` módszer módosíthatja az üzenetek láthatósági időtúlságát és tartalmát. Az üzenet tartalmának UTF-8 kódolású karakterláncnak kell lennie, amely legfeljebb 64 KB méretű. Az új tartalommal együtt adja át a kód korábbi részében mentett üzenet értékeit. Az `saved_message` értékek azonosítják a frissítandó üzenetet.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása várólistából

A korábban hozzáadott üzenetek letöltése a [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metódus hívásával.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Üzenetek törlése várólistából

Üzenetek törlése a várólistából a fogadás és feldolgozás után. Ebben az esetben a feldolgozás csak az üzenet megjelenítése a konzolon.

Az alkalmazás szünetel a `input` felhasználói bevitel hez, ha feldolgozza és törli az üzeneteket, és feldolgozza és törli a felhasználókat. Ellenőrizze az [Azure Portalon,](https://portal.azure.com) hogy az erőforrások megfelelően jöttek-e létre, mielőtt törölné őket. A kifejezetten nem törölt üzenetek végül ismét láthatóvá válnak a várólistában, hogy újabb esélyt kaphassanak azok feldolgozására.

Adja hozzá ezt a `try` kódot a blokk végéhez:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Üzenetsor törlése

A következő kód törli az okat az erőforrásokat, amelyeket az alkalmazás a [delete_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metódussal történő törlésével hozott létre.

Adja hozzá ezt a `try` kódot a blokk végéhez, és mentse a fájlt:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre, és három üzenetet ad hozzá egy Azure-várólistához. A kód felsorolja az üzeneteket a várólistában, majd beolvassa és törli őket, mielőtt véglegesen törli a várólistát.

A konzolablakban keresse meg a *queues-quickstart-v12.py* fájlt tartalmazó könyvtárat, majd hajtsa végre a következő `python` parancsot az alkalmazás futtatásához.

```console
python queues-quickstart-v12.py
```

Az alkalmazás kimenete hasonló a következő példához:

```output
Azure Queue storage v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Amikor az alkalmazás szünetel az üzenetek fogadása előtt, ellenőrizze a tárfiókot az [Azure Portalon.](https://portal.azure.com) Ellenőrizze, hogy az üzenetek a várólistában vannak-e.

Az **Üzenetek billentyűlenyomásaz** üzenetek fogadásához és törléséhez. Amikor a rendszer kéri, nyomja le ismét az **Enter** billentyűt a várólista törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre várólistát, és hogyan adhat hozzá üzeneteket a Python-kód használatával. Ezután megtanulta betekinteni, beolvasni és törölni az üzeneteket. Végül megtanulta, hogyan kell törölni egy üzenetvárólistát.

Oktatóanyagok, minták, gyorsindítások és egyéb dokumentációk esetén látogasson el a következő oldalra:

> [!div class="nextstepaction"]
> [Azure Python-fejlesztőknek](https://docs.microsoft.com/azure/python/)

* További információ: [Az Azure Storage-kódtárak pythonhoz.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
* További Azure Queue storage mintaalkalmazások megtekintéséhez folytassa az [Azure Queue storage v12 Python-ügyfélkódtár-mintákat.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)
