---
title: Az Azure Event Hubs Capture bemutatója |} A Microsoft Docs
description: Ez a minta azt használja az Azure Python SDK-val az Event Hubs rögzítés funkciója.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2018
ms.author: shvija
ms.openlocfilehash: 0733d26839cbb2dc40f4dd99b60d6a5628cc1f1a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727086"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs Capture forgatókönyv: Python

Rögzítési funkciója az Azure Event hubs. Használhatja az automatikus továbbítását a streamelt adatokat az eseményközpont tetszőleges Azure Blob storage-fiókba. Ez a képesség megkönnyíti a kötegelt, valós idejű streamelési adatokkal kapcsolatos végrehajtásához. Ez a cikk ismerteti, hogyan használható az Event Hubs Capture Python használatával. Az Event Hubs Capture kapcsolatos további információkért lásd: a [áttekintő cikkben](event-hubs-capture-overview.md).

Ebben a példában a [Azure Python SDK](https://azure.microsoft.com/develop/python/) a rögzítés funkció bemutatásához. A sender.py program szimulált környezeti telemetriát küld az Event Hubs JSON formátumban. Az event hubs rögzítési funkciója használja ezeket az adatokat a Blob storage kötegekben van konfigurálva. A capturereader.py alkalmazás beolvassa az ilyen blobok és eszközönként egy hozzáfűző fájlt hoz létre. Az alkalmazás ezután írja az adatokat tartalmazó .csv fájlt.

## <a name="what-youll-accomplish"></a>Mit kell elvégezni

1. Hozzon létre egy Azure Blob storage-fiókot és a benne található blob-tároló az Azure portal használatával.
2. Event Hubs-névtér létrehozása az Azure portal használatával.
3. Hozzon létre egy event hubs rögzítési funkciója engedélyezve van, az Azure portal használatával.
4. Adatok küldése az event hubs egy Python-szkript használatával.
5. Olvassa el a fájlokat a capture mappából, és feldolgozza őket egy másik Python-szkript használatával.

## <a name="prerequisites"></a>Előfeltételek

- Python 2.7.x
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- Az aktív [az Event Hubs-névtér és az eseményközpont](event-hubs-create.md). 
- Engedélyezése **rögzítése** található utasításokat követve az event hub funkció: [engedélyezze az Event Hubs Capture az Azure portal használatával](event-hubs-capture-enable-through-portal.md)

## <a name="create-an-azure-blob-storage-account"></a>Azure Blob storage-fiók létrehozása
1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A portál bal oldali panelen válassza ki a **új** > **tárolási** > **Tárfiók**.
3. Végezze el a beállításokat a **storage-fiók létrehozása** ablaktáblán, és válassza ki **létrehozás**.
   
   !["A storage-fiók létrehozása" panel][1]
4. Miután látta, a **üzembe helyezések sikerültek** üzenet, válassza ki az új tárfiókot, majd a nevét a **Essentials** ablaktáblán, és válassza ki **Blobok**. Ha a **Blob service** panel megnyílik, válassza ki **+ tároló** tetején. A tároló neve **rögzítése**, majd zárja be a **Blob service** ablaktáblán.
5. Válassza ki **hozzáférési kulcsok** a bal oldali panelen, másolja, a tárfiók nevét és értékét az **key1**. Mentse ezeket az értékeket a Jegyzettömbbe vagy egy másik ideiglenes helyre.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Hozzon létre egy Python-szkriptet küldhet eseményeket az eseményközpontjába
1. Nyissa meg a Python kedvenc szerkesztőjében, például [Visual Studio Code][Visual Studio Code].
2. Hozzon létre egy parancsfájlt nevű **sender.py**. Ez a szkript küldi az 200 eseményeket az eseményközpontjába. Egyszerű környezeti olvasmányok elküldött JSON-fájlban.
3. Sender.py illessze be a következő kódot:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Frissítse az előző kód a névtér nevét, a kulcs értéke és az eseményközpont neve, az Event Hubs-névtér létrehozásakor beszerzett.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Hozzon létre egy Python-szkriptet a rögzítési fájlok olvasását

1. Adja meg a panelen, és válasszon **létrehozás**.
2. Hozzon létre egy parancsfájlt nevű **capturereader.py**. Ez a szkript beolvassa a rögzített fájlok, és létrehoz egy fájlt az adatok csak az adott eszköz eszközönként.
3. Capturereader.py illessze be a következő kódot:
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
       reader = DataFileReader(open(filename, 'rb'), DatumReader())
       dict = {}
       for reading in reader:
           parsed_json = json.loads(reading["Body"])
           if not 'id' in parsed_json:
               return
           if not dict.has_key(parsed_json['id']):
               list = []
               dict[parsed_json['id']] = list
           else:
               list = dict[parsed_json['id']]
               list.append(parsed_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print 'Processor started using path: ' + os.getcwd()
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
   ```
4. Illessze be a megfelelő értékeket a tárfiók nevét és a kulcs hívásában `startProcessing`.

## <a name="run-the-scripts"></a>A szkriptek futtatása
1. Nyisson meg egy parancssort, amelynek az elérési út Python, és futtassa ezeket a parancsokat, előfeltételként szükséges Python-csomagok telepítéséhez:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Ha egy korábbi vagy **azure-storage** vagy **azure**, előfordulhat, hogy kell használnia a **--frissítése** lehetőséget.
   
   Emellett szüksége lehet az alábbi parancsot (a legtöbb esetben nem szükséges):
   
   ```
   pip install cryptography
   ```
2. Módosítsa a könyvtárat, bárhol is legyenek sender.py és capturereader.py menteni, és futtassa a következő parancsot:
   
   ```
   start python sender.py
   ```
   
   Ez a parancs futtatásához a küldő egy új Python-folyamat elindul.
3. Várjon néhány percet a rögzítési futtatásához. Írja be a következő parancsot az eredeti parancsablakban:
   
   ```
   python capturereader.py
   ```

   A rögzítési processzor a helyi könyvtárban töltheti le a tárfiók/tároló összes blobjának használ. Feldolgozza azokat is, amelyek nem üresek, és az eredményeket tartalmazó .csv-fájlként a helyi könyvtárba ír.

## <a name="next-steps"></a>További lépések

További információ az Event Hubs az alábbi hivatkozások segítségével:

* [Az Event hubs – áttekintés rögzítése][Overview of Event Hubs Capture]
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs – áttekintés][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
