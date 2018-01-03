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
ms.openlocfilehash: cdbb2baea2bc6c45908369ff821c264b66053d95
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs rögzítése forgatókönyv: Python

Event Hubs rögzítése rendszerben, amely lehetővé teszi, hogy automatikusan a streamelési adatok az eseményközpont számára egy Azure Blob storage-fiókot az Ön által választott az Event hubs szolgáltatás. Ez a funkció megkönnyíti, hogy a valós idejű streamelési adatok kötegfeldolgozási végrehajtásához. A cikkből megtudhatja, hogyan használható az Event Hubs rögzítése a Python. További információ az Event Hubs rögzítése: a [a cikk áttekintése](event-hubs-capture-overview.md).

Ebben a példában a [Azure Python SDK](https://azure.microsoft.com/develop/python/) a rögzítése funkció bemutatásához. A sender.py program elküldi szimulált környezeti telemetriai Event Hubs JSON formátumban. Az event hubs blob-tároló kötegekben telepítse ezeket az adatokat írni az rögzítése funkció használatára van konfigurálva. A capturereader.py app majd beolvassa a blobok eszközönként egy hozzáfűző fájlt hoz létre, majd írja az adatokat .csv fájlokba.

## <a name="what-will-be-accomplished"></a>Az oktatóanyag célja

1. Hozzon létre egy Azure Blob Storage-fiókot és egy blob tároló belül, az Azure portál használatával.
2. Hozzon létre egy Eseményközpontba névtér, az Azure portál használatával.
3. Létrehoz egy eseményközpontot a rögzítési szolgáltatás engedélyezve van, az Azure portál használatával.
4. Adatok küldése az event hubs egy Python-parancsfájl.
5. Olvassa be a fájlok rögzítését, illetve dolgozza fel őket másik Python-parancsfájl.

## <a name="prerequisites"></a>Előfeltételek

- Python 2.7.x
- Azure-előfizetés
- Az aktív [Event Hubs névtér és az event hub.](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása
1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. A portál bal oldali navigációs ablaktábláján kattintson **új**, majd kattintson a **tárolási**, és kattintson a **Tárfiók**.
3. Töltse ki a mezőket a storage-fiók panelen, és kattintson a **létrehozása**.
   
   ![][1]
4. Miután meggyőződött arról a **központi telepítések sikeres** üzenet, kattintson a nevére, az új tárfiók és a a **Essentials** panelen kattintson a **Blobok**. Ha a **Blob szolgáltatás** panel nyílik meg, kattintson a **+ tároló** tetején. A tároló neve **rögzítése**, majd zárja be a **Blob szolgáltatás** panelen.
5. Kattintson a **hívóbetűk** a bal oldali panelen, és másolja a tárfiók nevét és értékét a **key1**. Ezeket az értékeket a Jegyzettömb vagy más ideiglenes helyre mentse.

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

1. Töltse ki a panelen, majd kattintson **létrehozása**.
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
          #content_length == 508 is an empty file, so only process content_length > 508 i.e. skip  empty files
          if blob.properties.content_length > 508:
              print('Downloaded a non empty blob: ' + blob.name)
              cleanName = string.replace(blob.name, '/', '_')
              block_blob_service.get_blob_to_path(container, blob.name, cleanName)
              processBlob(cleanName)
              os.remove(cleanName)
          block_blob_service.delete_blob(container, blob.name)
  startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
  ```
4. Ügyeljen arra, hogy illessze be a megfelelő értékeket a tárfiók nevét és a kulcs hívásában `startProcessing`.

## <a name="run-the-scripts"></a>A parancsfájlok futtatása
1. Nyisson meg egy parancssort, a Python a elérési úttal, és futtassa az előfeltételként szükséges Python-csomagok parancsok:
   
  ```
  pip install azure-storage
  pip install azure-servicebus
  pip install avro
  ```
   
  Ha egy korábbi vagy az azure-tároló, vagy az azure, szükség lehet használni a **--frissítése** beállítás
   
  Előfordulhat, hogy szükség futtassa a következő (a legtöbb rendszeren nem szükséges):
   
  ```
  pip install cryptography
  ```
2. Módosítsa a könyvtárat, ahol csak menteni szeretné sender.py és capturereader.py, és futtassa a parancsot:
   
  ```
  start python sender.py
  ```
   
  Ez a parancs futtatásához a küldő új Python folyamat elindul.
3. Most Várjon néhány percet a rögzítési futtatásához. Írja be a következő parancsot az eredeti parancs ablakba:
   
   ```
   python capturereader.py
   ```

   A rögzítési processzor a helyi címtárszolgáltatásban töltheti le a fiókot vagy a tároló összes blobjának használja. Feldolgozza azokat, amelyek nem üres, és kiírja az eredményeket a .csv fájlok a helyi könyvtárba.

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Az Event hubs – áttekintés rögzítése][Overview of Event Hubs Capture]
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs – áttekintés][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
