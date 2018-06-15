---
title: Az Azure Cosmos DB globális terjesztése
description: Megtudhatja, hogyan replikálhat adatokat globálisan az Azure Cosmos DB használatával az Azure Portalon
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 03/26/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: fb9418d47d2888467e1c1c40862833029111b75b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31431720"
---
Az Azure Cosmos DB globális terjesztését az alábbi videóból ismerheti meg, amelyben Andrew Liu, az Azure Cosmos DB programigazgatója mutatja be a globális terjesztés funkciót.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]

További információ a globális adatbázis-replikáció működéséről az Azure Cosmos DB szolgáltatásban: [Globális adatterjesztés a Cosmos DB-vel](../articles/cosmos-db/distribute-data-globally.md).

## <a id="addregion"></a>Globális adatbázis-régiók hozzáadása az Azure Portal használatával
Az Azure Cosmos DB világszerte minden [Azure-régióban][azureregions] elérhető. Miután kiválasztotta az adatbázisfiók alapértelmezett konzisztenciaszintjét, egy vagy több régiót társíthat hozzá (a választott alapértelmezett konzisztenciaszinttől és a globális terjesztés szükségleteitől függően).

1. Az [Azure Portalon](https://portal.azure.com/) a bal oldali sávon kattintson az **Azure Cosmos DB** lehetőségre.
2. Az **Azure Cosmos DB** oldalon válassza ki a módosítandó adatbázis-fiókot.
3. A fiók lapon kattintson az **Adatok globális replikálása** lehetőségre a menüben.
4. A térkép régióira, majd a **Mentés** gombra kattintva választhatja ki a hozzáadni vagy eltávolítani kívánt régiókat az **Adatok globális replikálása** lapon. A régiók hozzáadásának költsége van, további információkat az [árképzést ismertető oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/) vagy a [Globális adatterjesztés az Azure Cosmos DB-vel](../articles/cosmos-db/distribute-data-globally.md) című cikkben talál.
   
    ![Kattintson a térkép régióira azok hozzáadásához vagy eltávolításához][1]
    
Ha hozzáadta a második régiót, elérhetővé válik a **Manuális feladatátvétel** funkció a Portal **Adatok globális replikálása** oldalán. Ezzel a funkcióval tesztelhető a feladatátvételi folyamat vagy módosítható az elsődleges írási régió. Miután hozzáadta a harmadik régiót, ugyanazon az oldalon elérhetővé válik a **Feladatátvételi prioritások** funkció, így módosíthatja az olvasások feladatátvételi sorrendjét.  

### <a name="selecting-global-database-regions"></a>Globális adatbázis-régiók kiválasztása
Két vagy több régió konfigurálására két gyakori forgatókönyv áll rendelkezésre:

1. Kis késleltetésű adathozzáférés biztosítása a végfelhasználóknak, legyenek bárhol a világon
2. Regionális rugalmasság hozzáadása az üzletmenet-folytonosság és a vészhelyreállítás (BCDR) érdekében

Ha kis késleltetést szeretne biztosítani a végfelhasználók számára, javasolt, hogy az alkalmazást és az Azure Cosmos DB-t is abban a régióban való helyezze üzembe, amelyben az alkalmazások felhasználói találhatók.

A BCDR esetén javasolt az [Üzletmenet-folytonosság és vészhelyreállítás (BCDR): Az Azure párosított régiói][bcdr] című cikkben leírt régiópárok alapján hozzáadni a régiókat.

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
