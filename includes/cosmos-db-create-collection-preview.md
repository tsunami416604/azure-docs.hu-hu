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
ms.openlocfilehash: 331886f01345aba576cd8f96f95077f9bbdae704
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754309"
---
Hozzon létre egy adatbázist és egy tárolót az Azure Portalon az adatkezelő eszközét mostantól használhatja. 

1. Kattintson a **adatkezelő** > **új tároló**. 
    
    A **tároló hozzáadása** terület jobb szélen megjelenik, előfordulhat, hogy újra kell görgessen jobbra, hogy lássa.

    ![Az Azure portal adatkezelő, tároló hozzáadása panel](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. Az a **Hozzáadás tároló** lap, adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|Feladatok|Az új adatbázisnak adja a *Feladatok* nevet. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat /, \\, #, ? karaktereket vagy záró szóközt. Ellenőrizze a **kiépítése adatbázis átviteli** lehetőség, lehetővé teszi a különböző az adatbázison belül az összes tárolót az adatbázishoz létesített átviteli sebesség megosztásához. Ezt a lehetőséget is segít a költségek csökkentését. |
    |**Átviteli sebesség**|400|Hagyja meg az átviteli sebesség 400 kérelemegység / másodperc (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Adja meg *elemek* , az új tároló nevét. A Tárolóazonosítóknak ugyanazok a Karakterkorlátozások vonatkoznak, mint az adatbázis neve van.|
    |**Partíciókulcs**| /kategória| A cikkben bemutatott példa */category* partíciókulcsként. A partíciós kulcs beállítása lehetővé teszi, hogy a gyűjtemény igényeinek a tárolási és átviteli sebességet az alkalmazás méretezése az Azure Cosmos DB. Általában a partíciókulcs megfelelő választás, amelyik az egyenletes eloszlás tárolás és a kérés a kötet a számítási feladatok között számos különböző értékeket, és az eredmények szerepel. [További információ a particionálást.](../articles/cosmos-db/partitioning-overview.md)|
    
    Az előző beállításokon túl azt is megteheti **egyedi kulcsok** a tároló. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. Tároló létrehozása során egyedi létrehozásával egy vagy több értéket partíció kulcsonként egyediségének biztosítása érdekében. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](../articles/cosmos-db/unique-keys.md) kapcsolatos cikkben talál.
    
    Kattintson az **OK** gombra. Az adatkezelő megjeleníti az új adatbázist és a tároló.

