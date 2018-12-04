---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/19/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: c3cbfda674abaeea1adf35c3ee0d2b5ddf6b2f84
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853066"
---
Az Azure Portal Adatkezelő eszközét mostantól adatbázis és gyűjtemény létrehozására is használhatja. 

1. Kattintson az **Adatkezelő** > **Új gyűjtemény** elemre. 
    
    A jobb szélen megjelenik a **Gyűjtemény hozzáadása** terület. Előfordulhat, hogy jobbra kell görgetnie, hogy lássa.

    ![Az Azure Portal Adatkezelő, Gyűjtemény hozzáadása panel](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. A **Gyűjtemény hozzáadása** oldalon adja meg az új gyűjtemény beállításait.

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Adatbázis azonosítója|*Feladatok*|Az új adatbázisnak adja a *Feladatok* nevet. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat /, \\, #, ? karaktereket vagy záró szóközt.
    Katalógus azonosítója|*elemek*|Az új gyűjteménynek adja az *Elemek* nevet. A gyűjteményazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.
    Az adatbázis átviteli sebességének kiosztása|Hagyja üresen|Az Azure Cosmos DB oszthatnak ki átviteli kapacitásokat vagy az adatbázis szintjén (az adatbázis összes gyűjtemény megoszthatja az azonos átviteli) vagy a gyűjtemény szintjén. Hagyja üresen kiépítése átviteli sebesség, a gyűjtemény szintjén, adott gyűjteményhez.
    Tárkapacitás|*Korlátlan*|Válassza ki a Storage kapacitásának **korlátlan**. 
    Partíciókulcs|*/Category*|Adja meg "/ kategória" értéket partíciókulcsként. A partíciós kulcs beállítása lehetővé teszi, hogy a gyűjtemény igényeinek a tárolási és átviteli sebességet az alkalmazás méretezése az Azure Cosmos DB. Általában a partíciókulcs megfelelő választás, amelyik az egyenletes eloszlás tárolás és a kérés a kötet a számítási feladatok között számos különböző értékeket, és az eredmények szerepel. [További információ a particionálást.](../articles/cosmos-db/partitioning-overview.md)
    Teljesítmény|*400 Kérelemegység/s*|Módosítsa a teljesítményt másodpercenként 400 kérelemegységre (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében. 
    
    Az előző beállításokon túl azt is megteheti, hogy **egyedi kulcsokat** ad a gyűjteményhez. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. Ha egy gyűjtemény létrehozása során egyedi kulcsszabályzatot állít fel, azzal gondoskodhat róla, hogy egy vagy több érték egyedi legyen egy partíciókulcson belül. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](../articles/cosmos-db/unique-keys.md) kapcsolatos cikkben talál.
    
    Kattintson az **OK** gombra.

    Az Adatkezelő megjeleníti az új adatbázist és gyűjteményt.

    ![Az Azure Portal Adatkezelője, az új adatbázissal és gyűjteménnyel](./media/cosmos-db-create-collection/azure-cosmos-db-data-explorer-preview.png)