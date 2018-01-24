---
title: "Az Azure Event Hubs rögzítése forgatókönyv |} Microsoft Docs"
description: "Az Event Hubs rögzítése funkció használata bemutatása az Azure Python SDK-t használó mintaalkalmazás."
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 97cadbde2ddedade1a8688f1380b9ff9194613e7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs rögzítése forgatókönyv: Python

Rögzítési lehetővé teszi az Azure Event hubs. Automatikusan képes biztosítani a streamelési adatok számára egy Azure Blob storage-fiókot az Ön által választott az eseményközpont használható. Ez a funkció megkönnyíti, hogy a valós idejű streamelési adatok kötegfeldolgozási végrehajtásához. A cikkből megtudhatja, hogyan használható az Event Hubs rögzítése a Python. További információ az Event Hubs rögzítése: a [a cikk áttekintése](event-hubs-capture-overview.md).

Ebben a példában a [Azure Python SDK](https://azure.microsoft.com/develop/python/) a rögzítése funkció bemutatásához. A sender.py program elküldi szimulált környezeti telemetriai Event Hubs JSON formátumban. Az event hubs ezeket az adatokat a Blob storage kötegekben írni a rögzítése funkció használatára van konfigurálva. A capturereader.py app beolvassa a blobok és eszközönként egy hozzáfűző fájlt hoz létre. Az alkalmazás ezután ír az adatok CSV-fájlok.

## <a name="what-youll-accomplish"></a>Mit kell végrehajtani

1. Hozzon létre egy Azure Blob storage-fiókot és egy blob tárolóban, az Azure-portál használatával.
2. Az Event Hubs névtér létrehozása az Azure-portál használatával.
3. A rögzítési szolgáltatás engedélyezve van, az Azure portál használatával hozzon létre egy eseményközpontba.
4. Adatok küldése az event hubs egy Python-parancsfájl használatával.
5. A fájlok olvasásának a rögzítési és dolgozza fel őket másik Python-parancsfájl használatával.

## <a name="prerequisites"></a>Előfeltételek

- Python 2.7.x
- Azure-előfizetés
- Az aktív [Event Hubs névtér és az event hub](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-blob-storage-account"></a>Egy Azure Blob storage-fiók létrehozása
1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A portál bal oldali ablaktáblában jelölje ki **új** > **tárolási** > **Tárfiók**.
3. Végezze el a beállításokat a **storage-fiók létrehozása** ablaktáblán, és válassza ki azt **létrehozása**.
   
   !["A storage-fiók létrehozása" ablak][1]
4. Miután meggyőződött arról a **központi telepítések sikeres** üzenet, válassza ki az új tárfiók és a nevét a **Essentials** ablaktáblán, és válassza ki azt **Blobok**. Ha a **Blob szolgáltatás** ablak nyílik meg, válasszon **+ tároló** tetején. A tároló neve **rögzítése**, majd zárja be a **Blob szolgáltatás** ablaktáblán.
5. Válassza ki **hívóbetűk** a bal oldali ablaktáblán, és másolja a tárfiók nevét és értékét a **key1**. Ezeket az értékeket a Jegyzettömb vagy más ideiglenes helyre mentse.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>A Python-parancsfájl események küldése az eseményközpont létrehozása
1. Nyissa meg a kedvenc Python-szerkesztőt, például a [Visual Studio Code][Visual Studio Code].
2. Hozzon létre nevű parancsfájl **sender.py**. Ez a parancsfájl 200 események küldése az eseményközpont. Elküldött JSON egyszerű környezeti értékek.
3. Az alábbi kód beillesztése sender.py:
   
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
4. A névtér nevét, a kulcs értéke és az eseményközpont neveként az Event Hubs névtér létrehozása után beszerzett az előzőekben látható kód frissítése.

## <a name="create-a-python-script-to-read-your-capture-files"></a>A rögzítési fájljainak olvasása a Python-parancsfájl létrehozása

1. Töltse ki a panelen, és válasszon **létrehozása**.
2. Hozzon létre nevű parancsfájl **capturereader.py**. Ez a parancsfájl beolvassa a rögzített fájlok, és létrehoz egy fájlt eszközönként csak az eszköznek adatokat írni.
3. Az alábbi kód beillesztése capturereader.py:
   
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

## <a name="run-the-scripts"></a>A parancsfájlok futtatása
1. Nyisson meg egy parancssort, a Python a elérési úttal, és futtassa az előfeltételként szükséges Python-csomagok parancsok:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Ha egy korábbi vagy **azure-tároló** vagy **azure**, előfordulhat, hogy kell használnia a **--frissítése** lehetőséget.
   
   Is szükség lehet a következő parancsot (a legtöbb rendszeren nem szükséges):
   
   ```
   pip install cryptography
   ```
2. Módosítsa a könyvtárat, ahol csak menteni szeretné sender.py és capturereader.py, és futtassa a parancsot:
   
   ```
   start python sender.py
   ```
   
   Ez a parancs futtatásához a küldő új Python folyamat elindul.
3. Várjon néhány percet a rögzítési futtatásához. Írja be a következő parancsot az eredeti parancs ablakba:
   
   ```
   python capturereader.py
   ```

   A rögzítési processzor a helyi címtárszolgáltatásban töltheti le a fiókot vagy a tároló összes blobjának használja. Feldolgozza azokat, amelyek nem üres, és az eredmények .csv fájlok a helyi könyvtárba ír.

## <a name="next-steps"></a>További lépések

További információ az Event Hubs a következő hivatkozásokra:

* [Az Event hubs – áttekintés rögzítése][Overview of Event Hubs Capture]
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs – áttekintés][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
