---
title: Az Azure Event Hubs rögzített adatok beolvasása egy Python-alkalmazásból (legújabb)
description: Ebből a cikkből megtudhatja, hogyan írhat Python-kódot az Event hub számára elküldett adatok rögzítéséhez, és beolvashatja az Azure Storage-fiókból a rögzített események adatait.
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
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187240"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Event Hubs-információk rögzítése az Azure Storage-ban és a Python használatával (Azure-eventhub 5-ös verzió)

Beállíthat egy Event hub-t úgy, hogy az Event hub-nak elküldett, Azure Storage-fiókba vagy Azure Data Lake Storage 1. vagy 2. generációs adattárolóba beérkező adatkészletek rögzítve legyenek. Ez a cikk bemutatja, hogyan írhat Python-kódokat események küldéséhez az Event hub-ba, és beolvashatja a rögzített adatok **Azure Blob Storage**-ból való beolvasását. További információ erről a szolgáltatásról: [Event Hubs rögzítési funkciók áttekintése](event-hubs-capture-overview.md).

Ez a rövid útmutató az [Azure PYTHON SDK](https://azure.microsoft.com/develop/python/) használatával mutatja be a rögzítési funkciót. A *Sender.py* alkalmazás a szimulált környezeti telemetria JSON formátumban küldi az Event hubokba. Az event hubs rögzítési funkciója használja ezeket az adatokat a Blob storage kötegekben van konfigurálva. A *capturereader.py* alkalmazás beolvassa ezeket a blobokat, és létrehoz egy hozzáfűzési fájlt az egyes eszközökhöz. Az alkalmazás ezután a CSV-fájlokba írja az adatot.

> [!IMPORTANT]
> Ez a rövid útmutató az Azure Event Hubs Python SDK 5. verzióját használja. A Python SDK 1. verzióját használó gyors útmutatóért tekintse meg [ezt a cikket](event-hubs-capture-python.md). 

Ebben a rövid útmutatóban a következőket hajtja végre: 

> [!div class="checklist"]
> * Hozzon létre egy Azure Blob Storage-fiókot és-tárolót a Azure Portal.
> * Hozzon létre egy Event Hubs névteret a Azure Portal használatával.
> * Hozzon létre egy Event hub-t a rögzítési funkcióval, és kapcsolja össze a Storage-fiókkal.
> * Adatküldés az Event hub-ba egy Python-szkript használatával.
> * Fájlok olvasása és feldolgozása Event Hubs rögzítésből egy másik Python-parancsfájl használatával.

## <a name="prerequisites"></a>Előfeltételek

- Python 2,7 és 3,5 vagy újabb, a PIP telepítve és frissítve.  
- Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.  
- Aktív Event Hubs névtér és Event hub.
[Hozzon létre egy Event Hubs névteret és egy Event hubot a névtérben](event-hubs-create.md). Jegyezze fel a Event Hubs névtér nevét, az Event hub nevét és a névtér elsődleges elérési kulcsát. A hozzáférési kulcs beszerzéséhez tekintse meg [Event Hubs kapcsolati karakterlánc beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)című témakört. Az alapértelmezett kulcsnév a *RootManageSharedAccessKey*. Ebben a rövid útmutatóban csak az elsődleges kulcsra van szükség. Nincs szüksége a kapcsolatok karakterláncára.  
- Egy Azure Storage-fiók, egy blob-tároló a Storage-fiókban, valamint egy kapcsolódási sztring a Storage-fiókhoz. Ha nem rendelkezik ezekkel az elemekkel, tegye a következőket:  
    1. [Azure Storage-fiók létrehozása](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [BLOB-tároló létrehozása a Storage-fiókban](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [A Storage-fiókhoz tartozó kapcsolódási karakterlánc lekérése](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)

    Ügyeljen arra, hogy a rövid útmutató későbbi használatához a kapcsolódási karakterláncot és a tároló nevét jegyezze fel.  
- Az Event hub rögzítési funkciójának engedélyezése. Ehhez kövesse a [Event Hubs rögzítés engedélyezése a Azure Portal használatával](event-hubs-capture-enable-through-portal.md)című témakör utasításait. Válassza ki a Storage-fiókot és az előző lépésben létrehozott BLOB-tárolót. Az Event hub létrehozásakor is engedélyezheti a szolgáltatást.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Hozzon létre egy Python-szkriptet küldhet eseményeket az eseményközpontjába
Ebben a szakaszban egy Python-szkriptet hoz létre, amely 200 eseményt küld (10 eszköz * 20 eseményt) az Event hub-ba. Ezek az események JSON formátumban eljuttatott környezeti beolvasási példák. 

1. Nyissa meg kedvenc Python-szerkesztőjét, például a [Visual Studio Code][Visual Studio Code]-ot.
2. Hozzon létre egy *Sender.py*nevű szkriptet. 
3. Illessze be a következő kódot a *Sender.py*. 
   
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
    * Cserélje le a `EVENT HUBS NAMESPACE CONNECTION STRING`t a Event Hubs névtérhez tartozó kapcsolatok karakterláncára.  
    * Cserélje le a `EVENT HUB NAME`t az Event hub nevére.  
5. Futtassa a szkriptet az események az Event hubhoz való küldéséhez.  
6. A Azure Portalon ellenőrizheti, hogy az Event hub fogadta-e az üzeneteket. Váltson az **üzenetek** nézetre a **metrikák** szakaszban. Frissítse a lapot a diagram frissítéséhez. Eltarthat néhány másodpercig, amíg az oldal megjeleníti az üzenetek fogadását. 

    [![annak ellenőrzése, hogy az Event hub fogadta-e az üzeneteket](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Hozzon létre egy Python-szkriptet a rögzítési fájlok olvasását
Ebben a példában a rögzített adattárolók tárolása az Azure Blob Storage-ban történik. Az ebben a szakaszban található szkript beolvassa a rögzített adatfájlokat az Azure Storage-fiókjából, és CSV-fájlokat hoz létre, amelyekkel egyszerűen megnyithatja és megtekintheti azokat. Az alkalmazás aktuális munkakönyvtárában 10 fájl jelenik meg. Ezek a fájlok a 10 eszközre vonatkozó környezeti beolvasásokat fogják tartalmazni. 

1. A Python-szerkesztőben hozzon létre egy *capturereader.py*nevű szkriptet. Ez a szkript beolvassa a rögzített fájlokat, és létrehoz egy fájlt minden eszköz számára, hogy csak az adott eszközre írja az adatokat.
2. Illessze be a következő kódot a *capturereader.py*. 
   
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
3. Cserélje le a `AZURE STORAGE CONNECTION STRING`t az Azure Storage-fiókhoz tartozó kapcsolatok karakterláncára. Az ebben a rövid útmutatóban létrehozott tároló neve *rögzítve*lesz. Ha más nevet használt a tárolóhoz, a *rögzítés* helyére írja be a Storage-fiókban található tároló nevét. 

## <a name="run-the-scripts"></a>A szkriptek futtatása
1. Nyisson meg egy parancssort, amelynek az elérési út Python, és futtassa ezeket a parancsokat, előfeltételként szükséges Python-csomagok telepítéséhez:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Módosítsa a könyvtárat arra a könyvtárba, ahová a *Sender.py* és a *capturereader.py*mentette, majd futtassa a következő parancsot:
   
   ```
   python sender.py
   ```
   
   Ez a parancs futtatásához a küldő egy új Python-folyamat elindul.
3. Várjon néhány percet a rögzítés futtatására, majd írja be az alábbi parancsot az eredeti parancssorablakba:
   
   ```
   python capturereader.py
   ```

   Ez a rögzítési processzor a helyi könyvtár használatával tölti le az összes blobot a Storage-fiókból és-tárolóból. Feldolgozza azokat, amelyek nem üresek, és az eredményeket CSV-fájlként írja a helyi könyvtárba.

## <a name="next-steps"></a>Következő lépések
Tekintse [meg a Python-mintákat a githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
