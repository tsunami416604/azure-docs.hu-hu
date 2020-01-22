---
title: Az Azure Event Hubs rögzített adatok beolvasása a Python-alkalmazásból | Microsoft Docs
description: Ez a cikk bemutatja, hogyan írhat Python-kódot az Event hub számára elküldett adatok rögzítéséhez, és beolvashatja a rögzített események adatait egy Azure-tárolóból.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: 43223f7cb9ed254340c99d235d494d1e93583c7f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293538"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Az Azure Storage-ban tárolt Event Hubs-információk rögzítése és beolvasása a Python használatával 
Használhatja az Event hub konfigurálását is, hogy az Event hub-nak elküldett adattárolók egy Azure Storage-ban vagy Azure Data Lake Storageban legyenek rögzítve. Ez a cikk bemutatja, hogyan lehet az írási Python-kód használatával eseményeket küldeni az Event hub-ba, és beolvasni a rögzített adatok egy Azure Blob Storage-ból való beolvasását. A szolgáltatással kapcsolatos további információkért tekintse meg a [Event Hubs rögzítési funkcióinak áttekintése](event-hubs-capture-overview.md)című témakört.

Ez a példa az [Azure PYTHON SDK](https://azure.microsoft.com/develop/python/) használatával mutatja be a rögzítési funkciót. A sender.py program a szimulált környezeti telemetria JSON formátumban küldi Event Hubs. Az Event hub úgy van konfigurálva, hogy a rögzítési funkció használatával írja ezeket az adattárakba a blob Storage-ba. A capturereader.py alkalmazás beolvassa ezeket a blobokat, és létrehoz egy hozzáfűzési fájlt egy eszközön. Az alkalmazás ezután a. csv-fájlokba írja az adatot.

> [!IMPORTANT]
> Ez a rövid útmutató az Azure Event Hubs Python SDK 5. verzióját használja. A Python SDK régi 1. verzióját használó gyors üzembe helyezést [ebben a cikkben](event-hubs-capture-python.md)találja. Ha az SDK 1. verzióját használja, javasoljuk, hogy telepítse át a kódot a legújabb verzióra. Részletekért tekintse meg az [áttelepítési útmutatót](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).

Ebben a rövid útmutatóban a következőket hajtja végre: 

> [!div class="checklist"]
> * Hozzon létre egy Azure Blob Storage-fiókot és-tárolót a Azure Portal.
> * Hozzon létre egy Event Hubs névteret a Azure Portal használatával.
> * Hozzon létre egy Event hub-t a rögzítési funkcióval, és kapcsolja össze a Storage-fiókkal.
> * Adatküldés az Event hub-ba egy Python-szkript használatával.
> * Fájlok olvasása és feldolgozása Event Hubs rögzítésből egy másik Python-parancsfájl használatával.

## <a name="prerequisites"></a>Előfeltételek

- Python 2,7 és 3,5 vagy újabb, `pip` telepítve és frissítve.
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- [Hozzon létre egy Event Hubs névteret és egy Event hubot a névtérben](event-hubs-create.md). Jegyezze fel a Event Hubs névtér nevét, az Event hub nevét és a névtér elsődleges elérési kulcsát. A hozzáférési kulcs lekéréséhez kövesse a következő cikk utasításait: [kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az alapértelmezett kulcs neve: **RootManageSharedAccessKey**. Az oktatóanyaghoz nem szükséges a kapcsolatok karakterlánca. Csak az elsődleges kulcsra van szükség. 
- Az alábbi lépéseket követve hozzon létre egy **Azure Storage-fiókot** és egy **BLOB-tárolót**:
    1. [Hozzon létre egy Azure Storage-fiókot](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).
    2. [Hozzon létre egy BLOB-tárolót a tárolóban](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 
    3. [Szerezze be a kapcsolódási karakterláncot a Storage-fiókhoz](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string).

        Jegyezze fel a **kapcsolatok sztringjét** és a **tároló nevét**. Ezeket később a kódban fogja használni. 
- Az Event hub **rögzítési** funkciójának engedélyezéséhez kövesse az alábbi utasításokat: [Event Hubs Capture engedélyezése a Azure Portal használatával](event-hubs-capture-enable-through-portal.md). Válassza ki a Storage-fiókot és az előző lépésben létrehozott BLOB-tárolót. Az Event hub létrehozásakor is engedélyezheti a szolgáltatást. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Hozzon létre egy Python-szkriptet az események az Event hub-ba való küldéséhez
Ebben a szakaszban egy Python-szkriptet hoz létre, amely 200 eseményt küld (10 eszköz * 20 eseményt) az Event hub-ba. Ezek az események JSON formátumban továbbított környezeti olvasások. 

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code][Visual Studio Code]-ot.
2. Hozzon létre egy **Sender.py**nevű szkriptet. 
3. Illessze be a következő kódot a sender.py. A részletekért tekintse meg a kód megjegyzéseit. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. Cserélje le a következő értékeket a parancsfájlokban:
    1. Cserélje le a `EVENT HUBS NAMESPACE CONNECTION STRING`t a Event Hubs névtérhez tartozó kapcsolatok karakterláncára.
    2. Cserélje le a `EVENT HUB NAME`t az Event hub nevére. 
5. Futtassa a szkriptet az események az Event hubhoz való küldéséhez. 
6. A Azure Portalon ellenőrizheti, hogy az Event hub fogadta-e az üzeneteket. Váltson az **üzenetek** nézetre a **metrikák** szakaszban. Frissítse a lapot a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg meg nem jelenik az üzenetek fogadása. 

    [![annak ellenőrzése, hogy az Event hub fogadta-e az üzeneteket](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Python-szkript létrehozása a rögzítési fájlok olvasásához
Ebben a példában a rögzített adattárolást az Azure Blob Storage tárolja. Az ebben a szakaszban található szkript beolvassa az adatfájlokat az Azure Storage-ból, és CSV-fájlokat hoz létre, így egyszerűen megnyithatja és megtekintheti a tartalmat. Az alkalmazás aktuális munkakönyvtárában 10 fájl jelenik meg. Ezek a fájlok a 10 eszközre vonatkozó környezeti beolvasásokat fogják tartalmazni. 

1. A Python-szerkesztőben hozzon létre egy **capturereader.py**nevű szkriptet. Ez a szkript beolvassa a rögzített fájlokat, és az eszközön létrehoz egy fájlt, hogy csak az adott eszközre írja az adatokat.
2. Illessze be a következő kódot a capturereader.py. A részletekért tekintse meg a kód megjegyzéseit. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
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
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. Cserélje le a `<AZURE STORAGE CONNECTION STRING>`t az Azure Storage-fiókhoz tartozó kapcsolatok karakterláncára. Az oktatóanyagban létrehozott tároló neve: **Capture**. Ha a tárolóhoz más nevet használt, cserélje le a `capture` nevet a Storage-fiókban található tároló nevére. 

## <a name="run-the-scripts"></a>Parancsfájlok futtatása
1. Nyisson meg egy parancssort, amely a Python elérési útjában található, majd futtassa ezeket a parancsokat a Python előfeltételként szükséges csomagok telepítéséhez:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Módosítsa a könyvtárat, ahol a sender.py és a capturereader.py mentette, majd futtassa a következő parancsot:
   
   ```
   python sender.py
   ```
   
   Ez a parancs egy új Python-folyamatot indít el a küldő futtatásához.
3. Várjon néhány percet, amíg a rögzítés fut. Ezután írja be a következő parancsot az eredeti parancssorablakba:
   
   ```
   python capturereader.py
   ```

   Ez a rögzítési processzor a helyi könyvtár használatával tölti le az összes blobot a Storage-fiókból/tárolóból. Feldolgozza azokat, amelyek nem üresek, és az eredményeket. csv-fájlként írja a helyi könyvtárba.

## <a name="next-steps"></a>Következő lépések
Tekintse meg a Python-mintákat [a githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
