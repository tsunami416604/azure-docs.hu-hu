---
title: Azure Cosmos DB-fiókok használata
description: Ez a cikk az Azure Cosmos-fiókok létrehozását és használatát ismerteti. Az Azure Cosmos-fiókban található elemek hierarchiáját is megjeleníti
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/09/2020
ms.reviewer: sngun
ms.openlocfilehash: d01164552b1f3f10f592cedf80eafe57566bf09c
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661074"
---
# <a name="work-with-azure-cosmos-account"></a>Azure Cosmos-fiók használata

A Azure Cosmos DB egy teljes körűen felügyelt platform-szolgáltatás (Péter). A Azure Cosmos DB használatának megkezdéséhez először létre kell hoznia egy Azure Cosmos-fiókot az Azure-előfizetésében. Az Azure Cosmos-fiókja egyedi DNS-nevet tartalmaz, és az Azure Portal, az Azure CLI vagy más nyelvfüggő SDK-k használatával kezelheti a fiókokat. További információ: [Az Azure Cosmos-fiók kezelése](how-to-manage-database-account.md).

Az Azure Cosmos-fiók a globális terjesztés és a magas rendelkezésre állás alapvető egysége. Az adatok és az átviteli sebesség több Azure-régióban való globális elosztása érdekében bármikor hozzáadhat és eltávolíthat Azure-régiókat az Azure Cosmos-fiókjához. Beállíthatja, hogy az Azure Cosmos-fiók egyetlen vagy több írási régióval rendelkezzen. További információ: Azure- [régiók hozzáadása és eltávolítása Azure Cosmos-fiókjához](how-to-manage-database-account.md). Beállíthatja az [alapértelmezett konzisztencia](consistency-levels.md) -szintet az Azure Cosmos-fiókban. A Azure Cosmos DB átfogó SLA-kat biztosít, amely magában foglalja az átviteli sebességet, a késést a esetek 99% percentilis, a konzisztencia és a magas rendelkezésre állás érdekében. További információ: [Azure Cosmos db SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)-kat.

Az Azure Cosmos-fiókban lévő összes információhoz való hozzáférés biztonságos kezeléséhez használhatja a fiókhoz társított [elsődleges kulcsokat](secure-access-to-data.md) . Az adataihoz való további biztonságos hozzáféréshez konfigurálhat egy [VNET-szolgáltatási végpontot](vnet-service-endpoint.md) és egy [IP-tűzfalat](firewall-support.md) az Azure Cosmos-fiókjában. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elemek egy Azure Cosmos-fiókban

Az Azure Cosmos Container a méretezhetőség alapvető egysége. Gyakorlatilag korlátlan számú kiosztott átviteli sebesség (RU/s) és tárterület található a tárolón. Azure Cosmos DB transzparens módon particionálja a tárolót az Ön által megadott logikai partíciós kulcs használatával, hogy rugalmasan méretezheti a kiosztott átviteli sebességet és tárterületet. További információ: [Az Azure Cosmos-tárolók és-elemek használata](databases-containers-items.md).

Jelenleg legfeljebb 50 Azure Cosmos-fiókot hozhat létre az Azure-előfizetések alatt (ez egy, a támogatási kérésen keresztül növelhető enyhe korlát). Egyetlen Azure Cosmos-fiók gyakorlatilag korlátlan számú adatmennyiséget és kiosztott átviteli sebességet tud kezelni. Az adatok és a kiépített átviteli sebesség kezeléséhez létrehozhat egy vagy több Azure Cosmos-adatbázist a fiókjában és az adatbázison belül, létrehozhat egy vagy több tárolót is. Az alábbi képen egy Azure Cosmos-fiók elemeinek hierarchiája látható:

:::image type="content" source="./media/account-overview/hierarchy.png" alt-text="Azure Cosmos-fiók hierarchiája" border="false":::

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan kezelheti Azure Cosmos-fiókját és egyéb fogalmait:

* [Útmutató az Azure Cosmos-fiók kezeléséhez](how-to-manage-database-account.md)
* [Globális terjesztés](distribute-data-globally.md)
* [Konzisztenciaszintek](consistency-levels.md)
* [Azure Cosmos-tárolók és-elemek használata](databases-containers-items.md)
* [VNET szolgáltatási végpont az Azure Cosmos-fiókhoz](vnet-service-endpoint.md)
* [IP-tűzfal az Azure Cosmos-fiókhoz](firewall-support.md)
* [Útmutató Azure-régiók Azure Cosmos-fiókhoz való hozzáadásához és eltávolításához](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA-kat](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
