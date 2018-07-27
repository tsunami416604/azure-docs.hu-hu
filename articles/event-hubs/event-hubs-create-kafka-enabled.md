---
title: Hozzon létre Apache Kafka engedélyezve van az Azure Event Hubs |} A Microsoft Docs
description: Hozzon létre egy Kafka engedélyezve van az Azure Event Hubs-névtér az Azure portal használatával
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 79b6b879bd2332c044ce871e2c9a938c6b9c900c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285085"
---
# <a name="create-apache-kafka-enabled-event-hubs"></a>Az Apache Kafka engedélyezve van az event hubs létrehozása

Az Azure Event Hubs egy big Data típusú Adatstreamelési Platform (PaaS) szolgáltatás, amely másodpercenként több millió fogadnak és alacsony késéssel és nagy átviteli sebességet biztosít a valós idejű elemzési és vizualizációs szolgáltatás.

Az Azure Event Hubs egy Kafka-végpontot biztosít. Ez a végpont lehetővé teszi, hogy a natív módon megérteni az Event Hubs-névtér [Apache Kafka](https://kafka.apache.org/intro) üzenetek protokoll és API-k. Ez a képesség kommunikálhat az event hubs, mint a Kafka-témaköröket a protokollt használó ügyfelek módosítása, vagy saját fürtök futtatása nélkül. Az Event Hubs támogatja [Apache Kafka verziók 1.0](https://kafka.apache.org/10/documentation.html) és újabb verziók.

Ez a cikk ismerteti, hogyan hozzon létre egy Event Hubs-névtér és a Kafka-kompatibilis az event hubs Kafka alkalmazások való csatlakoztatásához szükséges kapcsolati sztring lekérése.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a feladatok megkezdése előtt.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Hozzon létre egy Kafka engedélyezve van az Event Hubs-névtér

1. Jelentkezzen be a [az Azure portal][Azure portal], és kattintson a **erőforrás létrehozása** , a képernyő bal felső.

2. Keresse meg az Event Hubs, és válassza ki a beállításokat itt látható:
    
    ![A portálon az Event Hubs keresése](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Adjon meg egy egyedi nevet, és engedélyezze a Kafka a névtérhez. Kattintson a **Create** (Létrehozás) gombra.
    
    ![Névtér létrehozása](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. A névtér létrehozása után a a **beállítások** lapon kattintson **megosztott elérési házirendek** , kérje le a kapcsolati karakterláncot.

    ![Kattintson a megosztott elérési szabályzatok](./media/event-hubs-create/create-event-hub7.png)

5. Kiválaszthatja az alapértelmezett **RootManageSharedAccessKey**, vagy adjon hozzá egy új házirendet. Kattintson a házirend nevére, és másolja a kapcsolati karakterláncot. 
    
    ![Válasszon ki egy szabályzatot](./media/event-hubs-create/create-event-hub8.png)
 
6. Adja hozzá ezt a kapcsolati karakterláncot a Kafka-alkalmazás konfigurációja.

Az Event hubsba a Kafka-protokollt használó alkalmazások már streamelheti eseményeket.

## <a name="next-steps"></a>További lépések

Az Event Hubs kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:

* [Stream az Event Hubsba, a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [További információ az Event Hubs, Kafka for](event-hubs-for-kafka-ecosystem-overview.md)
* [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
