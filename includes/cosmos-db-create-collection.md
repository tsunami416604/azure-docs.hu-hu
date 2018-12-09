---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: e287741fd6643c2eba192a9e29f46219faf520ec
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111849"
---
Az Azure Portal Adatkezelő eszközét mostantól adatbázis és gyűjtemény létrehozására is használhatja. 

1. Kattintson az **Adatkezelő** > **Új gyűjtemény** elemre. 
    
    A jobb szélen megjelenik a **Gyűjtemény hozzáadása** terület. Előfordulhat, hogy jobbra kell görgetnie, hogy lássa.

    ![Az Azure portal adatkezelő, gyűjtemény hozzáadása panel](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. A **Gyűjtemény hozzáadása** oldalon adja meg az új gyűjtemény beállításait.

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Adatbázis azonosítója|Feladatok|Az új adatbázisnak adja a *Feladatok* nevet. Adatbázis neve 1 és 255 karakter között kell tartalmaznia, és nem tartalmazhat `/, \\, #, ?`, vagy záró szóközt.
    Katalógus azonosítója|Elemek|Az új gyűjteménynek adja az *Elemek* nevet. A gyűjteményazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.
    Partíciókulcs| <Your partition key>| Írjon be egy partíciókulcsot, például */UserId*.
    Teljesítmény|400 kérelemegység|Módosítsa a teljesítményt másodpercenként 400 kérelemegységre (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében. 
    
    Az előző beállításokon túl azt is megteheti, hogy **egyedi kulcsokat** ad a gyűjteményhez. Ebben a példában az erre szolgáló mezőt hagyja üresen. Az egyedi kulcsok lehetőséget nyújtanak a fejlesztők számára, hogy adatintegritási réteget adjanak az adatbázishoz. Ha egy gyűjtemény létrehozása során egyedi kulcsszabályzatot állít fel, azzal gondoskodhat róla, hogy egy vagy több érték egyedi legyen egy partíciókulcson belül. További információt az [Azure Cosmos DB-ben egyedi kulcsaival](../articles/cosmos-db/unique-keys.md) kapcsolatos cikkben talál.
    
    Kattintson az **OK** gombra.

    Az Adatkezelő megjeleníti az új adatbázist és gyűjteményt.

    ![Az Azure Portal Adatkezelője, az új adatbázissal és gyűjteménnyel](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)