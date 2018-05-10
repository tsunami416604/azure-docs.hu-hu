---
title: Hozzon létre Kafka engedélyezve van az Azure Event Hubs |} Microsoft Docs
description: Hozzon létre egy Kafka engedélyezve van az Azure Event Hubs-névtér az Azure portál használatával
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 05/07/2018
ms.author: shvija
ms.openlocfilehash: 4f1d21be3c19dfbc764485fea47b6d4cb2171b3c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="create-kafka-enabled-event-hubs"></a>Hozzon létre Kafka engedélyezve van az Event Hubs

Az Azure Event Hubs egy adatfolyam-Platform (PaaS) szolgáltatás, amely több millió esemény / másodperc ingests, és alacsony késéssel és magas teljesítmény biztosít a valós idejű elemzési és a képi megjelenítés Big Data.

Az Azure Event Hubs Kafka ökoszisztéma a végpont biztosít. Ez a végpont lehetővé teszi, hogy a natív módon megérteni az Event Hubs névtér [Apache Kafka](https://kafka.apache.org/intro) üzenetek protokoll és API-k. Ezzel a lehetőséggel kommunikálhat az Event Hubs mint Kafka témakörök a protokollt használó ügyfelek módosítása, vagy saját fürtök futtatása nélkül. Az Event Hubs Kafka ökoszisztéma támogatja a [Apache Kafka verziók 1.0](https://kafka.apache.org/10/documentation.html) és újabb verziók.

Ez a cikk ismerteti az Event Hubs névtér létrehozása, és a kapcsolati karakterlánc Kafka alkalmazások Kafka való kapcsolódáshoz szükséges az Event Hubs engedélyezve van.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Hozzon létre egy Kafka engedélyezve van az Event Hubs névtér

1. Jelentkezzen be a [Azure-portálon][Azure portal], és kattintson a **hozzon létre egy erőforrást** , a képernyő bal felső.

2. Az Event Hubs és adja meg a beállításokat, itt látható:
    
    ![Az Event Hubs keressen rá a portálon](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. **Névtér létrehozása**, az így egy egyedi nevet, és engedélyezze a Kafka a névtéren. Kattintson a **Create** (Létrehozás) gombra.
    
    ![Névtér létrehozása](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. A névtér létrehozása után a a **beállítások** lapra, majd **megosztott elérési házirendek** lekérni a kapcsolati karakterláncot.

    ![Kattintson a megosztott elérési házirendek](./media/event-hubs-create/create-event-hub7.png)

5. Választhat, hogy az alapértelmezett **RootManageSharedAccessKey**, vagy adjon hozzá egy új házirendet. Kattintson a házirend nevére, és másolja a kapcsolati karakterláncot. 
    
    ![A szabályzat kiválasztása](./media/event-hubs-create/create-event-hub8.png)
 
6. Ez a kapcsolati karakterlánc hozzáadása a Kafka alkalmazás konfigurációja.

Most már a az Event Hubsban a Kafka protokollt használó alkalmazás adatfolyam formájában események.

## <a name="next-steps"></a>További lépések

Az Event Hubs kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:

* [Az Event Hubsban adatfolyam a Kafka alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [További tudnivalók az Event Hubs a Kafka ökoszisztéma](event-hubs-for-kafka-ecosystem-overview.md)
* [További tudnivalók az Event Hubs](event-hubs-what-is-event-hubs.md)


[Azure portal]: https://portal.azure.com/
