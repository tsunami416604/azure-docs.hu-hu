---
title: Eseménykezelők és úti célok – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Eseménykezelőket és úti célokat az Event Grid on Edge-ben
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849746"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Eseménykezelőket és úti célokat az Event Grid on Edge-ben

Az eseménykezelő az a hely, ahol az esemény további műveletet vagy az esemény feldolgozását. Az Event Grid on Edge modul, az eseménykezelő lehet ugyanazon a peremhálózati eszközön, egy másik eszközön, vagy a felhőben. Bármely WebHook használatával események kezelésére, vagy események küldése az egyik natív kezelők, például az Azure Event Grid.

Ez a cikk az egyes beállításokkal kapcsolatos információkat tartalmaz.

## <a name="webhook"></a>WebHook

A WebHook-végponton való közzétételhez állítsa be a `endpointType` következőt, `WebHook` és adja meg:

* endpointUrl: A WebHook végpont URL-címe

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Az Azure Event Grid felhőbeli végponton `endpointType` `eventGrid` való közzétételhez állítsa be a következőt, és adja meg:

* endpointUrl: Event Grid Topic URL-címe a felhőben
* sasKey: Az Event Grid Topic SAS-kulcsa
* topicName: Név az összes kimenő esemény eseményrácsba való bélyegéhez. A témakör neve akkor hasznos, ha eseményrácsos tartománytémakörbe szeretne fellépni.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                 "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
    }
   ```

## <a name="iot-edge-hub"></a>IoT peremhálózati központ

Az Edge Hub modulban való `endpointType` `edgeHub` közzétételhez állítsa be a következőt, és adja meg:

* outputName: Az a kimenet, amelyen az Event Grid modul az ezt az előfizetést az edgeHubhoz illeszkedő eseményeket irányítja. Például az alábbi előfizetésnek megfelelő eseményeket a /messages/modules/eventgridmodule/outputs/sampleSub4-be írja a rendszer.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "edgeHub",
              "properties": {
                "outputName": "sampleSub4"
              }
            }
          }
        }
    ```

## <a name="event-hubs"></a>Event Hubs

Ha közzé szeretne tenni egy `endpointType` `eventHub` Eseményközpontban, állítsa be a következőt, és adja meg:

* connectionString: A megosztott hozzáférési házirend által létrehozott adott eseményközpont kapcsolati karakterlánca.

    >[!NOTE]
    > A kapcsolati karakterláncnak entitásspecifikusnak kell lennie. A névtér kapcsolati karakterláncának használata nem fog működni. Létrehozhat egy entitásspecifikus kapcsolati karakterláncot, ha az Azure Portalon közzétenni kívánt eseményközpontra navigál, és a **megosztott hozzáférési szabályzatok** elemre kattint va, hogy új entitásspecifikus connecection-karakterláncot hozzon létre.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus által kezelt üzenetsorok

Ha közzé szeretne tenni egy `endpointType` szolgáltatásbusz-várólistában, állítsa be a következőt, `serviceBusQueue` és adja meg:

* connectionString: A megosztott hozzáférési házirend en keresztül létrehozott adott service bus-várólistához tartozó kapcsolati karakterlánc.

    >[!NOTE]
    > A kapcsolati karakterláncnak entitásspecifikusnak kell lennie. A névtér kapcsolati karakterláncának használata nem fog működni. Hozzon létre egy entitásspecifikus kapcsolati karakterláncot az Azure Portalon közzétenni kívánt adott Service Bus-várólistára való navigálással, és kattintson a **Megosztott hozzáférési szabályzatok** elemre egy új entitásspecifikus connecection karakterlánc létrehozásához.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Service Bus-üzenettémák

A Service Bus-témakörben való `endpointType` `serviceBusTopic` közzétételhez állítsa be a következőt, és adja meg:

* connectionString: Kapcsolati karakterlánc a megosztott hozzáférési szabályzat on keresztül létrehozott adott Service Bus-témakörhöz.

    >[!NOTE]
    > A kapcsolati karakterláncnak entitásspecifikusnak kell lennie. A névtér kapcsolati karakterláncának használata nem fog működni. Hozzon létre egy entitásspecifikus kapcsolati karakterláncot az Azure Portalon közzétenni kívánt adott Service Bus-témakörre való navigálással, és kattintson a **megosztott hozzáférési szabályzatok** elemre egy új entitásspecifikus connecection karakterlánc létrehozásához.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Tárolási üzenetsorok

A tárolóvárólistában való közzétételhez állítsa be a `endpointType` következőt, `storageQueue` és adja meg:

* queueName: Annak a tárolóvárólistának a neve, amelynek közzé teszi a közzétételt.
* connectionString: A storage-várólista által tárolt tárfiók kapcsolati karakterlánca.

    >[!NOTE]
    > Unline Event Hubs, Service Bus várólisták, és a Service Bus-témakörök, a tárolási várólisták használt kapcsolati karakterlánc nem entitásspecifikus. Ehelyett meg kell, de a kapcsolati karakterlánc a tárfiók.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```