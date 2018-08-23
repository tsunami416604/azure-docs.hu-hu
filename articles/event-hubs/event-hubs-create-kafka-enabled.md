---
title: Hozzon létre Apache Kafka engedélyezve van az Azure Event Hubs |} A Microsoft Docs
description: Hozzon létre egy Kafka engedélyezve van az Azure Event Hubs-névtér az Azure portal használatával
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: bahariri
ms.openlocfilehash: 7ce12f9dcaa15ade95274419f99c13d5915dbaaa
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42061394"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Az Apache Kafka engedélyezve van az event hubs létrehozása

Az Azure Event Hubs egy big Data típusú Adatstreamelési Platform (PaaS) szolgáltatás, amely másodpercenként több millió fogadnak és alacsony késéssel és nagy átviteli sebességet biztosít a valós idejű elemzési és vizualizációs szolgáltatás.

Az Azure Event Hubs egy Kafka-végpontot biztosít. Ez a végpont lehetővé teszi, hogy a natív módon megérteni az Event Hubs-névtér [Apache Kafka](https://kafka.apache.org/intro) üzenetek protokoll és API-k. Ez a képesség kommunikálhat az event hubs, mint a Kafka-témaköröket a protokollt használó ügyfelek módosítása, vagy saját fürtök futtatása nélkül. Az Event Hubs támogatja [Apache Kafka verziók 1.0](https://kafka.apache.org/10/documentation.html) és újabb verziók.

Ez a cikk ismerteti, hogyan hozzon létre egy Event Hubs-névtér és a Kafka-kompatibilis az event hubs Kafka alkalmazások való csatlakoztatásához szükséges kapcsolati sztring lekérése.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a feladatok megkezdése előtt.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka-kompatibilis Event Hubs-névtér létrehozása

1. Jelentkezzen be a [az Azure portal][Azure portal], és kattintson a **erőforrás létrehozása** , a képernyő bal felső.

2. Keressen rá az Event Hubsra, és válassza az itt látható lehetőségeket:
    
    ![Event Hubs keresése a portálon](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Adjon meg egy egyedi nevet, és engedélyezze a Kafkát a névtéren. Kattintson a **Create** (Létrehozás) gombra.
    
    ![Névtér létrehozása](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. A névtér létrehozását követően a **Beállítások** lap **Megosztott elérési szabályzatok** elemére kattintva kérje le a kapcsolati sztringet.

    ![Kattintás a Megosztott elérési szabályzatok elemre](./media/event-hubs-create/create-event-hub7.png)

5. Választhatja az alapértelmezett **RootManageSharedAccessKey** szabályzatot, vagy hozzáadhat egy újat is. Kattintson a szabályzat nevére, és másolja a vágólapra a kapcsolati sztringet. 
    
    ![Szabályzat kiválasztása](./media/event-hubs-create/create-event-hub8.png)
 
6. Adja hozzá ezt a kapcsolati sztringet a Kafka-alkalmazás konfigurációjához.

Így már streamelheti az eseményeket az Event Hubsba a Kafka-protokollt használó alkalmazásaiból.

## <a name="next-steps"></a>További lépések

Az Event Hubs kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:

* [Stream az Event Hubsba, a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [További információ az Event Hubs, Kafka for](event-hubs-for-kafka-ecosystem-overview.md)
* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
