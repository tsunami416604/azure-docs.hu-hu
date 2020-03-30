---
title: Az Azure Cosmos DB-fiókok kal való együttműködés
description: Ez a cikk ismerteti, hogyan hozza létre és használja az Azure Cosmos-fiókok. Azt is mutatja, az elemek hierarchia egy Azure Cosmos-fiók
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: d29ed68b2945b2473b33aa88176e6f5d832a0fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246902"
---
# <a name="work-with-azure-cosmos-account"></a>Azure Cosmos-fiók használata

Az Azure Cosmos DB egy teljes körűen felügyelt platform-szolgáltatásként (PaaS). Az Azure Cosmos DB használatának megkezdéséhez először hozzon létre egy Azure Cosmos-fiókot az Azure-előfizetésében. Az Azure Cosmos-fiók tartalmaz egy egyedi DNS-nevet, és kezelheti a fiókot az Azure Portal, az Azure CLI vagy különböző nyelvspecifikus SDK-k használatával. További információ: [Az Azure Cosmos-fiók kezelése.](how-to-manage-database-account.md)

Az Azure Cosmos-fiók a globális disztribúció és a magas rendelkezésre állás alapvető egysége. Az adatok és az átviteli műveletek globális elosztásához több Azure-régióban bármikor hozzáadhatja és eltávolíthatja az Azure Cosmos-fiókba az Azure Cosmos-fiókot. Konfigurálhatja az Azure Cosmos-fiókot, hogy egy vagy több írási régiók. További információ: [Azure-régiók hozzáadása és eltávolítása az Azure Cosmos-fiókhoz.](how-to-manage-database-account.md) Az Azure Cosmos-fiók [alapértelmezett konzisztenciaszintjét](consistency-levels.md) konfigurálhatja. Az Azure Cosmos DB átfogó SLA-kat biztosít, amelyek átfogó átviteli, késési a 99. További információ: [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Az Azure Cosmos-fiókon belüli összes adathoz való hozzáférés biztonságos kezeléséhez használhatja a fiókjához társított [főkulcsokat.](secure-access-to-data.md) Az adatokhoz való további biztonságos hozzáférés érdekében konfigurálhat egy [VNET-szolgáltatás végpontot](vnet-service-endpoint.md) és [IP-tűzfalat](firewall-support.md) az Azure Cosmos-fiókban. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elemek egy Azure Cosmos-fiókban

Az Azure Cosmos tároló a méretezhetőség alapvető egysége. Gyakorlatilag korlátlan kiosztott átviteli -fokkal (RU/s) rendelkezhet, és tárolót tárolhat egy tárolón. Az Azure Cosmos DB transzparens módon particionálja a tárolót a logikai partíciókulcs használatával, amely et a kiosztott átviteli és tárolási rugalmas méretezése érdekében adja meg. További információ: [Az Azure Cosmos-tárolók és -elemek használatával kapcsolatos témakörökben](databases-containers-items.md)talál.

Jelenleg legfeljebb 100 Azure Cosmos-fiókot hozhat létre egy Azure-előfizetés ben. Egyetlen Azure Cosmos-fiók gyakorlatilag korlátlan mennyiségű adatot és kiépített átviteli forgalmat képes kezelni. Az adatok és a kiépített átviteli nyomás kezeléséhez létrehozhat egy vagy több Azure Cosmos-adatbázist a fiókjában és az adatbázison belül, létrehozhat egy vagy több tárolót. Az alábbi képen az Azure Cosmos-fiók elemeinek hierarchiája látható:

![Egy Azure Cosmos-fiók hierarchiája](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan kezelheti az Azure Cosmos-fiókot és egyéb fogalmakat:

* [Az Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Globális terjesztés](distribute-data-globally.md)
* [Konzisztenciaszintek](consistency-levels.md)
* [Az Azure Cosmos-tárolók és -elemek közös munkálatai](databases-containers-items.md)
* [VNET-szolgáltatásvégpont az Azure Cosmos-fiókhoz](vnet-service-endpoint.md)
* [IP-tűzfal az Azure Cosmos-fiókhoz](firewall-support.md)
* [Azure-régiók hozzáadása és eltávolítása az Azure Cosmos-fiókhoz](how-to-manage-database-account.md)
* [Az Azure Cosmos DB SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
