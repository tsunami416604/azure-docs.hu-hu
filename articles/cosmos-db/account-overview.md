---
title: Az Azure Cosmos DB-fiókok használata
description: Ez a cikk azt ismerteti, hogyan hozzon létre, és az Azure Cosmos DB-fiókok használata
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: da55807d4ca803adf63a1dd2dfe3ce3794cdd509
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762599"
---
# <a name="work-with-azure-cosmos-account"></a>Azure Cosmos-fiók használata

Az Azure Cosmos DB egy teljes körűen felügyelt platform--szolgáltatásként (PaaS) a. Azure Cosmos DB használatának megkezdéséhez először az Azure-előfizetésében ajánlott létrehozni egy Azure Cosmos-fiókot. Az Azure Cosmos-fiók egy egyedi DNS-nevet tartalmazza, és a egy fiókot az Azure portal, Azure CLI használatával, vagy különböző nyelvfüggő SDK-k használatával kezelheti. További információkért lásd: [kezelése az Azure Cosmos-fiók](how-to-manage-database-account.md).

Az Azure Cosmos-fiók a globális terjesztés és a magas rendelkezésre állás alapvető egysége. Globális terjesztéséhez az adatok és az átviteli sebesség több Azure-régióban, hozzáadhat és az Azure-régiók az Azure Cosmos-fiókjába bármikor eltávolíthatja. Konfigurálhatja az Azure Cosmos-fiókot, hogy rendelkezik egy vagy több írási régiót. További információkért lásd: [hozzáadása és eltávolítása az Azure-régiók és az Azure Cosmos-fiók](how-to-manage-database-account.md). Konfigurálhatja a [alapértelmezett konzisztencia](consistency-levels.md) szint, az Azure Cosmos-fiók. Az Azure Cosmos DB átfogó SLA-k, amely magában foglalja az átviteli sebesség, a késés a 99. percentilis, a konzisztencia és a magas rendelkezésre állást biztosít. További információkért lásd: [Azure Cosmos DB SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Hozzáférés az Azure Cosmos-fiókban lévő összes adatot a biztonságos felügyeletéhez, használhatja a [főkulcsok](secure-access-to-data.md) a fiókjához. További biztonságos hozzáférés az adatokhoz, konfigurálhat egy [szolgáltatásvégpont](vnet-service-endpoint.md) és [IP-tűzfal](firewall-support.md) a Azure Cosmos-fiókjában. 

## <a name="elements-in-an-azure-cosmos-account"></a>Egy Azure Cosmos-fiókban lévő elemek

Az Azure Cosmos DB-tárolók méretezhetőség alapvető egysége. Egy tároló gyakorlatilag korlátlan kiosztott átviteli kapacitás (RU/s) és a storage is rendelkezhet. Az Azure Cosmos DB transzparens módon particionálja az Ön által megadott annak érdekében, hogy rugalmasan skálázhatja a kiosztott átviteli sebesség és a tárolási logikai partíciókulccsal tároló. További információkért lásd: [használata az Azure Cosmos-tárolók és elemek](databases-containers-items.md).

Jelenleg legfeljebb 100 Azure Cosmos-fiókok egy Azure-előfizetéshez is létrehozhat. Egy olyan Azure-Cosmos-fiók gyakorlatilag kezelheti korlátlan mennyiségű adat és átviteli sebességet biztosítanak. Az adatok és a létesített átviteli sebesség kezelésére, a fiók alatt és, hogy az adatbázis belül hozhat létre egy vagy több Azure-Cosmos-adatbázis, létrehozhat egy vagy több tárolókat. Az alábbi képen látható a hierarchia elemek egy Azure Cosmos-fiók:

![Egy Azure Cosmos-fiók hierarchiája](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kezelheti az Azure Cosmos-fiókját és más fogalmakat:

* [Útmutató az Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Globális terjesztés](distribute-data-globally.md)
* [Konzisztenciaszint](consistency-levels.md)
* [Az Azure Cosmos-tárolók és elemek használata](databases-containers-items.md)
* [Az Azure Cosmos-fiókja szolgáltatásvégpont](vnet-service-endpoint.md)
* [IP-tűzfal az Azure Cosmos-fiók](firewall-support.md)
* [Útmutató hozzáadása és eltávolítása az Azure-régiók és az Azure Cosmos-fiók](how-to-manage-database-account.md)
* [Az Azure Cosmos DB SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
