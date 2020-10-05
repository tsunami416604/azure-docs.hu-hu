---
title: fájlbefoglalás
description: fájlbefoglalás
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "68854655"
---
Az Azure Portal Adatkezelő eszközét mostantól adatbázis és tábla létrehozására is használhatja. 

1. Válassza ki **adatkezelő**  >  **új táblát**. 
    
    A jobb szélen megjelenik a **Táblázat hozzáadása** terület. Esetenként jobbra kell görgetnie, hogy lássa.

    ![Adatkezelő az Azure Portalon](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. A **Tábla hozzáadása** oldalon adja meg az új tábla beállításait.

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Táblaazonosító|minta tábla|Az új tábla azonosítója. A táblák nevére ugyanazok a korlátozások vonatkoznak, mint az adatbázis-azonosítókra. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat `/ \ # ?` karaktereket vagy záró szóközt.
    Teljesítmény|400 kérelemegység|Módosítsa a teljesítményt másodpercenként 400 kérelemegységre (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.

3. Válassza az **OK** lehetőséget.

4. Az Adatkezelő megjeleníti az új adatbázist és táblát.

   ![Az Azure Portal Adatkezelője, az új adatbázissal és gyűjteménnyel](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)