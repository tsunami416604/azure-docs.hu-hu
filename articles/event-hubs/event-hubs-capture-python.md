---
title: Rögzített adatok olvasása a Python-alkalmazásból – Azure Event Hubs | Microsoft Docs
description: Az Azure Python SDK-t használó parancsfájlok a Event Hubs rögzítési funkciójának bemutatására.
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
ms.custom: seodec18
ms.date: 10/10/2019
ms.author: shvija
ms.openlocfilehash: 354964e1b66b55dcccd9b5674f011f8c5a38a1c5
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428951"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs rögzítési útmutató: Python

A capture az Azure Event Hubs egyik funkciója. A capture használatával automatikusan továbbíthatja az adatátviteli adatait az Event hub-ban egy tetszőleges Azure Blob Storage-fiókba. Ez a funkció megkönnyíti a kötegelt feldolgozást a valós idejű adatfolyam-továbbítási adatátvitelhez. Ez a cikk azt ismerteti, hogyan használható a Event Hubs Capture a Python használatával. További információ a Event Hubs rögzítéséről: [események rögzítése az Azure Event Hubs használatával][Overview of Event Hubs Capture].

Ez az útmutató az [Azure PYTHON SDK](https://azure.microsoft.com/develop/python/) használatával mutatja be a rögzítési funkciót. A *Sender.py* program a szimulált környezeti telemetria JSON formátumban küldi Event Hubs. Az Event hub a rögzítési funkcióval írja be ezeket az adattárakba a blob Storage-ba. A *capturereader.py* alkalmazás beolvassa ezeket a blobokat, létrehoz egy hozzáfűzési fájlt az összes eszközhöz, és az adatokat *. csv* fájlba írja az egyes eszközökön.

Ebben az útmutatóban a következőket végezheti el: 

> [!div class="checklist"]
> * Hozzon létre egy Azure Blob Storage-fiókot és-tárolót a Azure Portal.
> * Engedélyezze Event Hubs rögzítést, és irányítsa azt a Storage-fiókjába.
> * Adatküldés az Event hub-ba egy Python-szkript használatával.
> * Fájlok olvasása és feldolgozása Event Hubs rögzítésből egy másik Python-parancsfájl használatával.

## <a name="prerequisites"></a>Előfeltételek

- Python 3,4 vagy újabb verzió, `pip` telepítéssel és frissítéssel.
  
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
  
- Egy aktív Event Hubs névtér és az Event hub, amelyet a gyors üzembe helyezési útmutatóban talál [: hozzon létre egy Event hub](event-hubs-create.md)-t a Azure Portal használatával. Jegyezze fel a névtér és az Event hub nevét az útmutató későbbi részében való használatra. 
  
  > [!NOTE]
  > Ha már rendelkezik tárolási tárolóval, akkor engedélyezheti a rögzítést, és kiválaszthatja a tárolót az Event hub létrehozásakor. 
  > 
  
- A Event Hubs megosztott elérési kulcs neve és az elsődleges kulcs értéke. Keresse meg vagy hozza létre ezeket az értékeket a Event Hubs oldalon található **megosztott hozzáférési házirendek** területen. Az alapértelmezett hozzáférési kulcs neve **RootManageSharedAccessKey**. Másolja a hozzáférési kulcs nevét és az elsődleges kulcs értékét, hogy az a forgatókönyv későbbi részében használhassa. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Azure Blob Storage-fiók és-tároló létrehozása

Hozzon létre egy Storage-fiókot és-tárolót, amelyet a rögzítéshez használni szeretne. 

1. Jelentkezzen be az [Azure portálra][Azure portal].
2. A bal oldali navigációs ablakban válassza a **Storage-fiókok**lehetőséget, majd a Storage- **fiókok** képernyőn válassza a **Hozzáadás**lehetőséget.
3. A Storage-fiók létrehozása képernyőn válassza ki az előfizetést és az erőforráscsoportot, majd adja meg a Storage-fiók nevét. Alapértelmezés szerint a többi beállítás is elhagyható. Válassza a **felülvizsgálat + létrehozás**lehetőséget, tekintse át a beállításokat, majd válassza a **Létrehozás**lehetőséget. 
   
   ![Storage-fiók létrehozása][1]
   
4. Az üzembe helyezés befejeztével válassza az **erőforrás**megnyitása lehetőséget, majd a Storage-fiók **áttekintése** képernyőn válassza a **tárolók**lehetőséget.
5. A **tárolók** képernyőn válassza a **+ tároló**elemet. 
6. Az **új tároló** képernyőn adja meg a tároló nevét, majd kattintson az **OK gombra**. Jegyezze fel a tároló nevét, amelyet később az útmutatóban kell használni. 
7. A **tárolók** képernyő bal oldali navigációs sávján válassza a **hozzáférési kulcsok**elemet. Másolja a **Storage-fiók nevét**és a **kulcs** értékét a **key1**alatt, hogy később használhassa a bemutatóban.
 
## <a name="enable-event-hubs-capture"></a>Event Hubs rögzítésének engedélyezése

1. A Azure Portal navigáljon az Event hub-hoz úgy, hogy kijelöli a Event Hubs névteret az **összes erőforrásból**, kiválasztja az **Event hubok** elemet a bal oldali navigációs sávon, majd kiválasztja az Event hub-t. 
2. Az Event hub **Áttekintés** képernyőjén válassza az **események rögzítése**lehetőséget.
3. A **rögzítési** képernyőn válassza **a**be lehetőséget. Ezután az **Azure Storage-tároló**területen válassza a **tároló kiválasztása**lehetőséget. 
4. A **tárolók** képernyőn válassza ki a használni kívánt tárolót, majd válassza a **kiválasztás**lehetőséget. 
5. A **rögzítési** képernyőn válassza a **módosítások mentése**elemet. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Hozzon létre egy Python-szkriptet az események az Event hubhoz való küldéséhez
Ez a szkript 200 eseményt küld az Event hub-nak. Az események a JSON-ban eljuttatott egyszerű környezeti olvasások.

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code][Visual Studio Code]-ot.
2. Hozzon létre egy új, *Sender.py*nevű fájlt. 
3. Illessze be a következő kódot a *Sender.py*. Helyettesítse be a saját értékeit a Event Hubs \<namespace >, \<AccessKeyName >, @no__t 2primary kulcs értékét > és \<eventhub >.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Mentse a fájlt.

## <a name="create-a-python-script-to-read-capture-files"></a>Python-szkript létrehozása a rögzítési fájlok olvasásához

Ez a szkript beolvassa a rögzített fájlokat, és létrehoz egy fájlt az egyes eszközökhöz, hogy csak az adott eszközre írja az adatokat.

1. A Python-szerkesztőben hozzon létre egy új, *capturereader.py*nevű fájlt. 
2. Illessze be a következő kódot a *capturereader.py*. Helyettesítse be a \<storageaccount >, a \<storage fiók elérési kulcs > és \<storagecontainer > mentett értékeit.
   
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
           if not parsed_json['id'] in dict:
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
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>A Python-parancsfájlok futtatása

1. Nyisson meg egy parancssort, amely a Python elérési útjában található, és futtassa a következő parancsokat a Python előfeltételként szükséges csomagok telepítéséhez:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Ha `azure-storage` vagy `azure` korábbi verziója van telepítve, lehetséges, hogy a `--upgrade` kapcsolót kell használnia.
   
   Előfordulhat, hogy az alábbi parancsot is futtatnia kell. A parancs futtatása nem szükséges a legtöbb rendszeren. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Futtassa ezt a parancsot abban a könyvtárban, ahol a *Sender.py* és a *capturereader.py*mentette:
   
   ```cmd
   start python sender.py
   ```
   
   A parancs egy új Python-folyamatot indít el a küldő futtatásához.
   
3. A rögzítés befejeződése után futtassa a következő parancsot:
   
   ```cmd
   python capturereader.py
   ```

   A rögzítési processzor letölti az összes nem üres blobot a Storage-fiók tárolójából, és az eredményeket *. csv* -fájlként írja a helyi könyvtárba. 

## <a name="next-steps"></a>Következő lépések

A Event Hubsról további információt a következő témakörben talál: 

* [A Event Hubs rögzítésének áttekintése][Overview of Event Hubs Capture]
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs – áttekintés][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
