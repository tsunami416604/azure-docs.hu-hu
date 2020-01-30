---
title: Eseménykezelők és célhelyek – Azure Event Grid IoT Edge | Microsoft Docs
description: Eseménykezelők és célhelyek a Event Grid Edge-ben
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849746"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Eseménykezelők és célhelyek a Event Grid Edge-ben

Az eseménykezelő az a hely, ahol az esemény további műveletre vagy az esemény feldolgozására szolgál. Az Edge-modul Event Grid az eseménykezelő ugyanazon a peremhálózati eszközön, egy másik eszközön vagy a felhőben is lehet. Az események kezeléséhez bármilyen webhookot használhat, vagy eseményeket küldhet az egyik natív kezelőhöz, például a Azure Event Gridhoz.

Ez a cikk a konfigurálásával kapcsolatos információkat tartalmaz.

## <a name="webhook"></a>WebHook

Webhook-végponton való közzétételhez állítsa a `endpointType` `WebHook` és adja meg a következőket:

* endpointUrl: a webhook végpontjának URL-címe

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

Azure Event Grid Felhőbeli végponton való közzétételhez állítsa be a `endpointType`t `eventGrid`re, és adja meg a következőket:

* endpointUrl: Event Grid témakör URL-címe a felhőben
* sasKey: Event Grid a témakör SAS-kulcsát
* topicName: az összes kimenő esemény Event Gridra való bélyegzésének neve. A témakör neve akkor lehet hasznos, ha Event Grid tartományi témakörre küldi el.

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

## <a name="iot-edge-hub"></a>IoT Edge hub

Az Edge hub-modulba való közzétételhez állítsa be a `endpointType`t `edgeHub`re, és adja meg a következőket:

* outputName: az a kimenet, amelyre a Event Grid modul továbbítja az előfizetésnek megfelelő eseményeket a edgeHub. Például az alábbi előfizetésnek megfelelő események a/messages/modules/eventgridmodule/outputs/sampleSub4.-be lesznek írva

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

## <a name="event-hubs"></a>Azure Event Hubs-eseményközpontok

Az Event hub-ban való közzétételhez állítsa be a `endpointType`t `eventHub`re, és adja meg a következőket:

* connectionString: az adott esemény központhoz tartozó kapcsolati karakterlánc, amelyet egy megosztott elérési házirend használatával generált.

    >[!NOTE]
    > A kapcsolatok karakterláncának entitás-specifikusnak kell lennie. A névtér-kapcsolatok karakterláncának használata nem fog működni. Létrehozhat egy entitás-specifikus kapcsolati karakterláncot úgy, hogy az Azure Portalon közzétenni kívánt esemény hubhoz navigál, és a **megosztott hozzáférési házirendek** elemre kattint egy új entitás-specifikus connecection-karakterlánc létrehozásához.

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

Service Bus üzenetsor közzétételéhez állítsa be a `endpointType`t `serviceBusQueue`re, és adja meg a következőket:

* connectionString: a megadott Service Bus-várólista kapcsolati karakterlánca, amelyet egy megosztott elérési házirend használatával generált.

    >[!NOTE]
    > A kapcsolatok karakterláncának entitás-specifikusnak kell lennie. A névtér-kapcsolatok karakterláncának használata nem fog működni. Hozzon elő egy entitás-specifikus kapcsolati karakterláncot úgy, hogy az Azure Portalon közzétenni kívánt Service Bus várólistára navigál, és a **megosztott hozzáférési házirendek** elemre kattintva létrehoz egy új entitás-specifikus connecection karakterláncot.

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

Service Bus témakör közzétételéhez állítsa be a `endpointType`t `serviceBusTopic`re, és adja meg a következőket:

* connectionString: a megadott Service Bus témakör kapcsolati karakterlánca, amelyet egy megosztott elérési házirend használatával generált.

    >[!NOTE]
    > A kapcsolatok karakterláncának entitás-specifikusnak kell lennie. A névtér-kapcsolatok karakterláncának használata nem fog működni. Hozzon elő egy entitás-specifikus kapcsolati karakterláncot, és navigáljon az Azure Portalon közzétenni kívánt Service Bus témakörre, majd kattintson a **megosztott hozzáférési házirendek** elemre egy új entitás-specifikus connecection-karakterlánc létrehozásához.

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

## <a name="storage-queues"></a>Tárolási várólisták

A Storage-üzenetsor közzétételéhez állítsa a `endpointType` `storageQueue` és adja meg a következőket:

* queueName: az a tárolási várólista neve, amelyet közzé szeretne tennie.
* connectionString: a Storage-fiókhoz tartozó kapcsolati sztring a tárolási üzenetsor.

    >[!NOTE]
    > A Event Hubs, Service Bus a várólisták és a Service Bus témakörök nem használhatók, mert a tárolási várólistákhoz használt kapcsolatok karakterlánca nem entitás-specifikus. Ehelyett a Storage-fiókhoz tartozó kapcsolatok karakterláncát kell megadnia.

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