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
ms.openlocfilehash: a91c42ca32fb356b418dcd412c0690b01ff85789
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908136"
---
Az Azure Portal Adatkezelő eszközét mostantól adatbázis és tábla létrehozására is használhatja. 

1. Kattintson az **Adatkezelő** > **Új tábla** parancsra. 
    
    A jobb szélen megjelenik a **Táblázat hozzáadása** terület. Esetenként jobbra kell görgetnie, hogy lássa.

    ![Adatkezelő az Azure Portalon](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. A **Tábla hozzáadása** oldalon adja meg az új tábla beállításait.

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Táblaazonosító|minta tábla|Az új tábla azonosítója. A táblák nevére ugyanazok a korlátozások vonatkoznak, mint az adatbázis-azonosítókra. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat `/ \ # ?` karaktereket vagy záró szóközt.
    Teljesítmény|400 kérelemegység|Módosítsa a teljesítményt másodpercenként 400 kérelemegységre (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.

3. Kattintson az **OK** gombra.

4. Az Adatkezelő megjeleníti az új adatbázist és táblát.

   ![Az Azure Portal Adatkezelője, az új adatbázissal és gyűjteménnyel](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)