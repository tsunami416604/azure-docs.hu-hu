---
title: 'Rövid útmutató: Rögzített adatok olvasása a Python alkalmazásból – Azure Event Hubs'
description: 'Rövid útmutató: Parancsfájlok, amelyek az Azure Python SDK-t használják az Event Hubs Capture funkció bemutatásához.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: 6c830cf871c2ae650bb61e8b3712a664e9e405d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187291"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Rövid útmutató: Event Hubs Capture forgatókönyv: Python (azure-eventhub 1-es verzió)

A capture az Azure Event Hubs egyik szolgáltatása. A Rögzítés segítségével automatikusan kézbesítheti a streamelési adatokat az eseményközpontban egy ön által kiválasztott Azure Blob-tárfiókba. Ez a funkció megkönnyíti a kötegelt feldolgozást a valós idejű streamelési adatokon. Ez a cikk ismerteti, hogyan használhatja az Event Hubs Capture python használatával. Az Event Hubs-rögzítésről az [Események rögzítése az Azure Event Hubs-on keresztül][Overview of Event Hubs Capture]című témakörben talál további információt.

Ez a forgatókönyv az [Azure Python SDK-t](https://azure.microsoft.com/develop/python/) használja a rögzítési funkció bemutatásához. A *sender.py* program szimulált környezeti telemetriai adatokat küld az Event Hubs JSON formátumban. Az eseményközpont a Rögzítés szolgáltatás segítségével írja ezeket az adatokat a Blob storage-ba kötegekben. A *capturereader.py* alkalmazás beolvassa ezeket a blobokat, minden eszközéhez létrehoz egy hozzáfűző fájlt, és minden eszközön *.csv* fájlokba írja az adatokat.

> [!WARNING]
> Ez a rövid útmutató az Azure Event Hubs Python SDK 1-es verziójához. Javasoljuk, hogy [telepítse át](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) a kódot a Python [SDK 5-ös verziójába.](get-started-capture-python-v2.md)

Ebben a forgatókönyvben a következőket teszi: 

> [!div class="checklist"]
> * Hozzon létre egy Azure Blob-tárfiókot és -tárolót az Azure Portalon.
> * Engedélyezze az Event Hubs Rögzítést, és irányítsa azt a tárfiókba.
> * Adatok küldése az eseményközpontba egy Python-parancsfájl használatával.
> * Fájlok olvasása és feldolgozása az Event Hubs Capture egy másik Python-parancsfájl használatával.

## <a name="prerequisites"></a>Előfeltételek

- Python 3.4-es `pip` vagy újabb, telepített és frissített.
  
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
  
- Egy aktív Event Hubs névtér és eseményközpont, amelyet a rövid útmutató utasításainak követésével hoztak [létre: Hozzon létre egy eseményközpontot az Azure Portal használatával.](event-hubs-create.md) Jegyezze fel a névtér és az eseményközpont neveit, amelyeket a forgatókönyv későbbi részében használhat. 
  
  > [!NOTE]
  > Ha már rendelkezik egy tárolótárolóval, engedélyezheti a rögzítést, és kiválaszthatja a tárolótárolót az Event Hub létrehozásakor. 
  > 
  
- Az Event Hubs megosztotta a hozzáférési kulcs nevét és az elsődleges kulcs értékét. Ezeket az értékeket az Event Hubs-lapon található **Megosztott hozzáférési házirendek** csoportban találja meg vagy hozza létre. Az alapértelmezett hozzáférési kulcs neve **RootManageSharedAccessKey**. Másolja a hozzáférési kulcs nevét és a forgatókönyv későbbi részében használandó elsődleges kulcs értéket. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Azure Blob-tárfiók és -tároló létrehozása

Hozzon létre egy tárfiókot és tárolót a rögzítéshez. 

1. Jelentkezzen be az [Azure Portalra.][Azure portal]
2. A bal oldali navigációs sávon válassza a **Tárfiókok**lehetőséget, majd a **Tárfiókok** képernyőn válassza a **Hozzáadás lehetőséget.**
3. A tárfiók létrehozása képernyőn válasszon ki egy előfizetést és egy erőforráscsoportot, és adjon nevet a tárfióknak. A többi kijelölést alapértelmezetten hagyhatja. Válassza **a Véleményezés + létrehozás**lehetőséget, tekintse át a beállításokat, majd válassza a **Létrehozás lehetőséget.** 
   
   ![Storage-fiók létrehozása][1]
   
4. Amikor a központi telepítés befejeződött, válassza az Ugrás az **erőforrásra**lehetőséget, és a tárfiók **áttekintése** képernyőn válassza a **Tárolók**lehetőséget.
5. A **Tárolók** képernyőn válassza a **+ Tároló**lehetőséget. 
6. Az **Új tároló** képernyőn adjon nevet a tárolónak, majd kattintson az **OK gombra.** Jegyezze fel a tároló nevét, amelyet a forgatókönyv későbbi részében szeretne használni. 
7. A **Tárolók** képernyő bal oldali navigációs képernyőjén válassza az **Access billentyűk**lehetőséget. Másolja a **Storage-fiók nevét**és a **kulcs1**alatti kulcs alatti **kulcs** értéket a forgatókönyv későbbi részében való használatra.
 
## <a name="enable-event-hubs-capture"></a>Eseményközpontok rögzítésének engedélyezése

1. Az Azure Portalon keresse meg az eseményközpontot az Event Hubs névtér **kiválasztásával az összes erőforrás**közül, válassza **az Eseményközpontok a** bal oldali navigációs sávban, majd válassza ki az eseményközpontot. 
2. Az eseményközpont **áttekintése** képernyőn válassza az **Események rögzítése**lehetőséget.
3. A **Rögzítés** képernyőn válassza a **Be**lehetőséget. Ezután az **Azure Storage Container csoportban**válassza **a Tároló kiválasztása lehetőséget.** 
4. A **Tárolók** képernyőn jelölje ki a használni kívánt tárolótárolót, majd válassza **a Kijelölés lehetőséget.** 
5. A **Rögzítés** képernyőn válassza a **Módosítások mentése lehetőséget.** 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Python-parancsfájl létrehozása események küldéséhez az Event Hubra
Ez a parancsfájl 200 eseményt küld az eseményközpontba. Az események egyszerű környezeti értékek küldött JSON.

1. Nyissa meg kedvenc Python-szerkesztőjét, például [a Visual Studio-kódot.][Visual Studio Code]
2. Hozzon létre egy új fájlt *sender.py*néven. 
3. Illessze be a következő kódot *sender.py*. Helyettesítse saját értékeit \<az Event \<Hubs névtér>, az AccessKeyName>, \<az elsődleges kulcs érték> és \<az eventhub>.
   
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

## <a name="create-a-python-script-to-read-capture-files"></a>Python-parancsfájl létrehozása a rögzítési fájlok olvasásához

Ez a parancsfájl beolvassa a rögzített fájlokat, és létrehoz egy fájlt az egyes eszközök számára, hogy csak az adott eszközre írhassa az adatokat.

1. A Python-szerkesztőben hozzon létre egy *új fájlt,* amelynek neve capturereader.py. 
2. Illessze be a következő kódot *capturereader.py*. Helyettesítse a \<mentett értékeket \<a tárfiók>, a tárfiók hozzáférési kulcs> és \<a storagecontainer>.
   
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

1. Nyisson meg egy parancssort, amelynek a Python az útjában van, és futtassa ezeket a parancsokat a Python-előfeltétel csomagok telepítéséhez:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Ha a vagy `azure-storage` `azure`a korábbi verziójával rendelkezik, `--upgrade` akkor lehet, hogy használnia kell ezt a lehetőséget.
   
   Előfordulhat, hogy a következő parancsot is futtatnia kell. A parancs futtatása a legtöbb rendszeren nem szükséges. 
   
   ```cmd
   pip install cryptography
   ```
   
2. A sender.py és *capturereader.py* *capturereader.py*könyvtárból futtassa a következő parancsot:
   
   ```cmd
   start python sender.py
   ```
   
   A parancs új Python-folyamatot indít el a feladó futtatásához.
   
3. Amikor a rögzítés befejeződik, futtassa a következő parancsot:
   
   ```cmd
   python capturereader.py
   ```

   A rögzítési processzor letölti az összes nem üres blobot a tárfiók tárolójából, és az eredményeket *.csv* fájlként írja a helyi könyvtárba. 

## <a name="next-steps"></a>További lépések

Az Eseményközpontokról az: 

* [Az Eseményközpontok rögzítésének áttekintése][Overview of Event Hubs Capture]
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Eseményközpontok – áttekintés][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
