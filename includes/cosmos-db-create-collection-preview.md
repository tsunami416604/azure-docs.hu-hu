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
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "68002673"
---
Most már használhatja a Azure Portal Adatkezelő eszközét egy adatbázis és egy tároló létrehozásához. 

1. Kattintson **adatkezelő** > **új tárolóra**. 
    
    A jobb szélen megjelenik a **tároló hozzáadása** felület, ezért a jobb oldali görgetéshez jobbra kell görgetni a megjelenítéshez.

    ![A Azure Portal Adatkezelő, tároló hozzáadása panel](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection-preview.png)

2. A **tároló hozzáadása** lapon adja meg az új tároló beállításait.

    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|Feladatok|Az új adatbázisnak adja a *Feladatok* nevet. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat /, \\, #, ? karaktereket vagy záró szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Átviteli sebesség**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Adja meg az *elemeket* az új tároló neveként. A tároló-azonosítók ugyanazokkal a karakterekkel rendelkeznek, mint az adatbázis neve.|
    |**Partíciós kulcs**| /kategória| A cikkben ismertetett minta a */category* használja a partíciós kulcsként. A partíciós kulcs beállítása lehetővé teszi, hogy Azure Cosmos DB a gyűjtemény méretezését, hogy megfeleljen az alkalmazás tárolási és átviteli igényeinek. Általánosságban elmondható, hogy a partíciós kulcs jó választás, amely számos különböző értékkel rendelkezik, és a tárterület egyenletes eloszlását eredményezi, és a számítási feladatok mennyiségét is lekéri. [További információ a particionálásról.](../articles/cosmos-db/partitioning-overview.md)|
    
    Az előző beállításokon kívül opcionálisan hozzáadhat **egyedi kulcsokat** a tárolóhoz. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. A tárolók létrehozásakor egyedi kulcsokra vonatkozó szabályzat létrehozásával biztosíthatja, hogy a partíciós kulcs egy vagy több értéke egyedi legyen. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](../articles/cosmos-db/unique-keys.md) kapcsolatos cikkben talál.
    
    Kattintson az **OK** gombra. Adatkezelő megjeleníti az új adatbázist és tárolót.

