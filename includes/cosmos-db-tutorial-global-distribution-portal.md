---
title: Az Azure Cosmos DB globális terjesztése
description: Megtudhatja, hogyan replikálhat adatokat globálisan az Azure Cosmos DB használatával az Azure Portalon
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 12/26/2018
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 751571820d52c003a7e740bd63af8c9d9e071c7a
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796113"
---
## <a id="addregion"></a>Globális adatbázis-régiók hozzáadása az Azure Portal használatával
Az Azure Cosmos DB érhető el az összes [Azure-régiók] [ azureregions] világszerte. Miután kiválasztotta az adatbázisfiók alapértelmezett konzisztenciaszintjét, egy vagy több régiót társíthat hozzá (a választott alapértelmezett konzisztenciaszinttől és a globális terjesztés szükségleteitől függően).

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

A BCDR, ajánlott a régiópárok leírtak alapján régiók felvételére a [üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR): Az Azure párosított régiói] [ bcdr] cikk.

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
