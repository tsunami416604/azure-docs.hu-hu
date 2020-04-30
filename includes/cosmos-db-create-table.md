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
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68854655"
---
Az Azure Portal Adatkezelő eszközét mostantól adatbázis és tábla létrehozására is használhatja. 

1. Válassza ki **adatkezelő** > **új táblát**. 
    
    A jobb szélen megjelenik a **Táblázat hozzáadása** terület. Esetenként jobbra kell görgetnie, hogy lássa.

    ![Adatkezelő az Azure Portalon](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. A **Tábla hozzáadása** oldalon adja meg az új tábla beállításait.

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Táblaazonosító|minta tábla|Az új tábla azonosítója. A táblák nevére ugyanazok a korlátozások vonatkoznak, mint az adatbázis-azonosítókra. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat `/ \ # ?` karaktereket vagy záró szóközt.
    Átviteli sebesség|400 kérelemegység|Módosítsa a teljesítményt másodpercenként 400 kérelemegységre (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.

3. Kattintson az **OK** gombra.

4. Az Adatkezelő megjeleníti az új adatbázist és táblát.

   ![Az Azure Portal Adatkezelője, az új adatbázissal és gyűjteménnyel](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)