---
title: Blob Storage használata ellenőrzőpont-tárolóként az Azure Stack Hubon (előzetes verzió)
description: Ez a cikk ismerteti, hogyan használhatja a Blob Storage ellenőrzőpont-tárolóként az Azure Stack Hub eseményközpontokban (előzetes verzió).
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 2938099383c32eac493e4b4bb620f03c76ca5c44
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023653"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Blob Storage használata ellenőrzőpont-tárolóként – Eseményközpontok az Azure Stack Hubon (előzetes verzió)
Ha az Azure Blob Storage-t használja ellenőrzőpont-tárolóként egy olyan környezetben, amely a Storage Blob SDK egy másik verzióját támogatja, mint az Azure-ban általában elérhető, akkor kódot kell használnia a Storage service API-verziójának az adott környezet által támogatott verzióra történő módosításához. Ha például [az Event Hubs szolgáltatást egy 2002-es Azure Stack Hub-verzión futtatja,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)a Storage szolgáltatás legmagasabb elérhető verziója a 2017-11-09-es verzió. Ebben az esetben a Storage service API-verziójának 2017-11-09-re való célzásához kódot kell használnia. Egy adott Storage API-verzió célzásával kapcsolatos példát tekintse meg ezeket a mintákat a GitHubon: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) vagy [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python - [Szinkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [Aszinkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Az Azure Stack Hub Eseményközpontjai jelenleg [előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érhető el, és ingyenes. 

Ha olyan Event Hubs-fogadót futtat, amely a Blob Storage-t használja ellenőrzőpont-tárolóként anélkül, hogy az Azure Stack Hub által támogatott verziót célozná meg, a következő hibaüzenet jelenik meg:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Példahibaüzenet a Pythonban
A Python esetében `azure.core.exceptions.HttpResponseError` a hiba átkerül `on_error(partition_context, error)` `EventHubConsumerClient.receive()`a hibakezelőhöz. De a `receive()` módszer nem jelent kivételt. `print(error)`a következő kivételadatokat nyomtatja ki:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

A naplózó két figyelmeztetést naplóz, mint például a következőket:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>További lépések

Tekintse meg a következő cikket a particionálásról és az ellenőrzőpontok [ról: A partícióterhelés elosztása az alkalmazás több példányában](event-processor-balance-partition-load.md)
