---
title: Apache Kafka engedélyezett Event hub létrehozása – Azure Event Hubs | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre egy Apache Kafka engedélyezett Azure Event Hubs-névteret a Azure Portal használatával.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 9ce0f74ec6d4e536bfb3fe827ae6f8ae143b640e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555802"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Apache Kafka engedélyezett Event hubok létrehozása

Az Azure Event Hubs egy nagy adatátviteli platform, amely másodpercenként több millió eseményt tesz elérhetővé, és alacsony késést és nagy átviteli sebességet biztosít a valós idejű elemzésekhez és vizualizációhoz.

Az Azure Event Hubs egy Kafka-végpontot biztosít. Ez a végpont lehetővé teszi, hogy a Event Hubs névteret natív módon tudja értelmezni [Apache Kafka](https://kafka.apache.org/intro) üzenetküldési protokoll és API-k. Ezzel a képességgel a Kafka-témakörökhöz hasonlóan tud kommunikálni az Event hubokkal a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. A Event Hubs a Apache Kafka 1,0-es és újabb [verzióit](https://kafka.apache.org/10/documentation.html) támogatja.

Ez a cikk azt ismerteti, hogyan hozhat létre Event Hubs névteret, és hogyan kérheti le a Kafka-alkalmazások a Kafka-kompatibilis Event hubokhoz való csatlakoztatásához szükséges kapcsolati karakterláncot.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a feladatok megkezdése előtt.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka-kompatibilis Event Hubs-névtér létrehozása

1. Jelentkezzen be a [Azure Portalba][Azure portal], és kattintson a képernyő bal felső részén található **erőforrás létrehozása** elemre.

2. Keressen rá az Event Hubsra, és válassza az itt látható lehetőségeket:
    
    ![Event Hubs keresése a portálon](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Adjon meg egy egyedi nevet, és engedélyezze a Kafkát a névtéren. Kattintson a  **Create** (Létrehozás) gombra.
    
    ![Névtér létrehozása](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.jpg)
 
4. A névtér létrehozását követően a **Beállítások** lap **Megosztott elérési szabályzatok** elemére kattintva kérje le a kapcsolati sztringet.

    ![Kattintás a Megosztott elérési szabályzatok elemre](./media/event-hubs-create/create-event-hub7.png)

5. Választhatja az alapértelmezett **RootManageSharedAccessKey** szabályzatot, vagy hozzáadhat egy újat is. Kattintson a szabályzat nevére, és másolja a vágólapra a kapcsolati sztringet. 
    
    ![Szabályzat kiválasztása](./media/event-hubs-create/create-event-hub8.png)
 
6. Adja hozzá ezt a kapcsolati sztringet a Kafka-alkalmazás konfigurációjához.

Így már streamelheti az eseményeket az Event Hubsba a Kafka-protokollt használó alkalmazásaiból.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Event Hubsről, tekintse meg a következő hivatkozásokat:

* [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Ismerkedés a Kafkához készült Event Hubs szolgáltatással](event-hubs-for-kafka-ecosystem-overview.md)
* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
