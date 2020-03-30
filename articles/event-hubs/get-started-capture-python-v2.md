---
title: Az Azure Event Hubs rögzített adatainak olvasása Python-alkalmazásból (legújabb)
description: Ez a cikk bemutatja, hogyan írhat Python-kódot az eseményközpontba küldött adatok rögzítéséhez és a rögzített eseményadatok olvasásához egy Azure storage-fiókból.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 34583ef49b2f919391af3fe5700a558b2dc40700
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187240"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Az Event Hubs-adatok rögzítése az Azure Storage-ban és a Python használatával (az azure-eventhub 5-ös verziójával)

Konfigurálhat egy eseményközpontot úgy, hogy az eseményközpontba küldött adatok at egy Azure storage-fiókban vagy az Azure Data Lake Storage Gen 1 vagy Gen 2-ben rögzítsék. Ez a cikk bemutatja, hogyan írhat Python-kódot az események eseményközpontba küldéséhez és a rögzített adatok olvasásához az **Azure Blob storage-ból.** A funkcióról további információt az [Eseményközpontok rögzítése funkció – áttekintés című témakörben talál.](event-hubs-capture-overview.md)

Ez a rövid útmutató az [Azure Python SDK-t](https://azure.microsoft.com/develop/python/) használja a rögzítési funkció bemutatásához. A *sender.py* alkalmazás szimulált környezeti telemetriai adatokat küld a JSON formátumú eseményközpontoknak. Az eseményközpont úgy van beállítva, hogy a Rögzítés szolgáltatás használatával kötegekben írja ezeket az adatokat a Blob storage-ba. A *capturereader.py* alkalmazás beolvassa ezeket a blobokat, és minden eszközhöz létrehoz egy hozzáfűző fájlt. Az alkalmazás ezután csv-fájlokba írja az adatokat.

> [!IMPORTANT]
> Ez a rövid útmutató az Azure Event Hubs Python SDK 5-ös verzióját használja. A Python SDK 1-es verzióját használó rövid útmutatót ebben a [cikkben](event-hubs-capture-python.md)olvashatja. 

Ebben a rövid útmutatóban a következőket hajtja végre: 

> [!div class="checklist"]
> * Hozzon létre egy Azure Blob-tárfiókot és -tárolót az Azure Portalon.
> * Hozzon létre egy Event Hubs névteret az Azure Portal használatával.
> * Hozzon létre egy eseményközpontot úgy, hogy a Rögzítés funkció engedélyezve van, és csatlakoztassa a tárfiókhoz.
> * Adatok küldése az eseményközpontba egy Python-parancsfájl használatával.
> * Fájlok olvasása és feldolgozása az Event Hubs Capture egy másik Python-parancsfájl használatával.

## <a name="prerequisites"></a>Előfeltételek

- Python 2.7 és 3.5 vagy újabb, a PIP telepítve és frissítve.  
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.  
- Aktív Event Hubs névtér és eseményközpont.
[Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot a névtérben.](event-hubs-create.md) Jegyezze fel az Event Hubs névtér nevét, az eseményközpont nevét és a névtér elsődleges hozzáférési kulcsát. A hozzáférési kulcs leválasztásához olvassa el Az [Eseményközpontok kapcsolati karakterláncának beszerezhető témakört.](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) Az alapértelmezett kulcsnév *RootManageSharedAccessKey*. Ehhez a rövid útmutatóhoz csak az elsődleges kulcsra van szükség. Nincs szüksége a kapcsolati karakterláncra.  
- Egy Azure storage-fiók, egy blob tároló a tárfiókban, és egy kapcsolati karakterláncot a tárfiókhoz. Ha nem rendelkezik ezekkel az elemekkel, tegye a következőket:  
    1. [Azure Storage-fiók létrehozása](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [Blob-tároló létrehozása a tárfiókban](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [A kapcsolati karakterlánc beszereznie a tárfiókhoz](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)

    Ügyeljen arra, hogy rögzítse a kapcsolati karakterláncot és a tároló nevét későbbi használatra ebben a rövid útmutatóban.  
- Engedélyezze a Rögzítés funkciót az eseményközponthoz. Ehhez kövesse az [Eseményközpontok rögzítésének engedélyezése az Azure Portal használatával című](event-hubs-capture-enable-through-portal.md)útmutató utasításait. Válassza ki a tárfiókot és az előző lépésben létrehozott blob-tárolót. A szolgáltatás eseményközpont létrehozásakor is engedélyezhető.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Python-parancsfájl létrehozása események küldéséhez az eseményközpontba
Ebben a szakaszban hozzon létre egy Python-parancsfájlt, amely 200 eseményt (10 eszköz * 20 eseményt) küld egy eseményközpontba. Ezek az események egy minta környezeti leolvasás, hogy a küldött JSON formátumban. 

1. Nyissa meg kedvenc Python-szerkesztőjét, például [a Visual Studio-kódot.][Visual Studio Code]
2. *Hozzon*létre egy sender.py nevű parancsfájlt. 
3. Illessze be a következő kódot *sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Cserélje le a következő értékeket a parancsfájlokban:  
    * Cserélje `EVENT HUBS NAMESPACE CONNECTION STRING` le az Event Hubs névtér kapcsolati karakterláncával.  
    * Cserélje `EVENT HUB NAME` le az eseményközpont nevét.  
5. Futtassa a parancsfájlt az események nek az eseményközpontba küldéséhez.  
6. Az Azure Portalon ellenőrizheti, hogy az eseményközpont megkapta-e az üzeneteket. Váltson **az Üzenetek** nézetre a **Metrikák** szakaszban. A lap frissítése a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg a lap megjeleníti, hogy az üzenetek érkeztek. 

    [![Annak ellenőrzése, hogy az eseményközpont megkapta-e az üzeneteket](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Python-parancsfájl létrehozása a rögzítési fájlok olvasásához
Ebben a példában a rögzített adatok az Azure Blob storage tárolja. Ebben a szakaszban a parancsfájl beolvassa a rögzített adatfájlokat az Azure storage-fiókjából, és CSV-fájlokat hoz létre, amelyeket könnyedén megnyithat és megtekinthet. Az alkalmazás aktuális munkakönyvtárában 10 fájl jelenik meg. Ezek a fájlok tartalmazzák a 10 eszköz környezeti értékeit. 

1. A Python-szerkesztőben hozzon létre egy *capturereader.py*nevű parancsfájlt. Ez a parancsfájl beolvassa a rögzített fájlokat, és létrehoz egy fájlt az egyes eszközök számára, hogy csak az adott eszközre írhassa az adatokat.
2. Illessze be a következő kódot *capturereader.py*. 
   
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
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Cserélje `AZURE STORAGE CONNECTION STRING` le az Azure storage-fiókjához való kapcsolati karakterláncot. A rövid útmutatóban létrehozott tároló neve *rögzítés.* Ha a tárolóhoz más nevet használt, cserélje le a *rögzítést* a tároló nevére a tárfiókban. 

## <a name="run-the-scripts"></a>A parancsfájlok futtatása
1. Nyisson meg egy parancssort, amelynek a Python az útjában van, majd futtassa ezeket a parancsokat a Python-beli előfeltétel-csomagok telepítéséhez:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Módosítsa a könyvtárat arra a könyvtárra, ahová *sender.py* és *capturereader.py*mentett, és futtassa ezt a parancsot:
   
   ```
   python sender.py
   ```
   
   Ez a parancs egy új Python-folyamatot indít el a feladó futtatásához.
3. Várjon néhány percet, amíg a rögzítés futni fog, majd írja be a következő parancsot az eredeti parancsablakba:
   
   ```
   python capturereader.py
   ```

   Ez a rögzítési processzor a helyi könyvtárat használja az összes blob letöltéséhez a tárfiókból és a tárolóból. Feldolgozza azokat, amelyek nem üresek, és az eredményeket CSV-fájlként írja a helyi könyvtárba.

## <a name="next-steps"></a>További lépések
Tekintse meg [a Python-mintákat a GitHubon.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples) 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
