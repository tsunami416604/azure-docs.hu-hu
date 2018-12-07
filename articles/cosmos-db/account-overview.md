---
title: Az Azure Cosmos DB-fiókok használata
description: Ez a cikk azt ismerteti, hogyan hozzon létre, és az Azure Cosmos DB-fiókok használata
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 9f237d9eed5b8500f0f80b8b73d638f374bf5ca9
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999515"
---
# <a name="working-with-azure-cosmos-account"></a>Azure Cosmos-fiók használata

Az Azure Cosmos DB egy teljes körűen felügyelt platform--szolgáltatásként (PaaS) a. Azure Cosmos DB használatának megkezdéséhez először az Azure-előfizetésében ajánlott létrehozni egy Azure Cosmos-fiókot. Az Azure Cosmos-fiók egy egyedi DNS-nevet tartalmazza, és a egy fiókot az Azure portal, Azure CLI használatával, vagy különböző nyelvfüggő SDK-k használatával kezelheti. További információkért lásd: [kezelése az Azure Cosmos-fiók](how-to-manage-database-account.md).

Az Azure Cosmos-fiók a globális terjesztés és a magas rendelkezésre állás alapvető egysége. Globális terjesztéséhez az adatok és az átviteli sebesség több Azure-régióban, hozzáadhat és az Azure-régiók az Azure Cosmos-fiókjába bármikor eltávolíthatja. Konfigurálhatja az Azure Cosmos-fiókot, hogy rendelkezik egy vagy több írási régiót. További információkért lásd: [hozzáadása és eltávolítása az Azure-régiók és az Azure Cosmos-fiók](how-to-manage-database-account.md). Konfigurálhatja a [alapértelmezett konzisztencia](consistency-levels.md) szint, az Azure Cosmos-fiók. Az Azure Cosmos DB átfogó SLA-k, amely magában foglalja az átviteli sebesség, a késés a 99. percentilis, a konzisztencia és a magas rendelkezésre állást biztosít. További információkért lásd: [Azure Cosmos DB SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Biztonságosan kezelheti az Azure Cosmos-fiókban lévő összes adatot a hozzáférést, használhatja a főkulcsok a fiókjához. További biztonságos hozzáférés az adatokhoz konfigurálhatja egy szolgáltatásvégpont és IP-tűzfal a Azure Cosmos-fiókjában. 

## <a name="elements-in-an-azure-cosmos-account"></a>Egy Azure Cosmos-fiókban lévő elemek

Az Azure Cosmos DB-tárolók méretezhetőség alapvető egysége. Egy tároló gyakorlatilag korlátlan kiosztott átviteli kapacitás (RU/s) és a storage is rendelkezhet. Az Azure Cosmos DB transzparens módon particionálja az Ön által megadott annak érdekében, hogy rugalmasan skálázhatja a kiosztott átviteli sebesség és a tárolási logikai partíciókulccsal tároló. További információkért lásd: [használata az Azure Cosmos-tárolók és elemek](databases-containers-items.md).

Jelenleg legfeljebb 100 Azure Cosmos-fiókok egy Azure-előfizetéshez is létrehozhat. Egy olyan Azure-Cosmos-fiók gyakorlatilag kezelheti korlátlan mennyiségű adat és átviteli sebességet biztosítanak. Az adatok és a létesített átviteli sebesség kezelésére, a fiók alatt és, hogy az adatbázis belül hozhat létre egy vagy több Azure-Cosmos-adatbázis, létrehozhat egy vagy több tárolókat. Az alábbi képen látható a hierarchia elemek egy Azure Cosmos-fiók:

![Egy Azure Cosmos-fiók hierarchiája](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>További lépések

Folytassa az Azure Cosmos-fiók kezeléséhez, vagy tekintse meg a többi Azure Cosmos DB-vel kapcsolatos fogalmak megismeréséhez:

* [Útmutató az Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Globális terjesztés](distribute-data-globally.md)
* [Konzisztenciaszint](consistency-levels.md)
* [Az Azure Cosmos-tárolók és elemek használata](databases-containers-items.md)
* [Az Azure Cosmos-fiókja szolgáltatásvégpont](vnet-service-endpoint.md)
* [IP-tűzfal az Azure Cosmos-fiók](firewall-support.md)
* [Útmutató hozzáadása és eltávolítása az Azure-régiók és az Azure Cosmos-fiók](how-to-manage-database-account.md)
* [Az Azure Cosmos DB SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
