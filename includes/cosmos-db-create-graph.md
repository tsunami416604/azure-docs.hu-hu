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
ms.openlocfilehash: 837231ca07abcfdbd6ce932bb24bd890d91506a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69541485"
---
Az Azure Portal Adatkezelő eszközét mostantól gráfadatbázisok létrehozására is használhatja. 

1. Válassza **az Adatkezelő** > **új grafikonját**.

    A jobb szélen megjelenik a **Gráf hozzáadása** terület. Előfordulhat, hogy jobbra kell görgetnie, hogy lássa.

    ![Az Azure Portal Adatkezelő, Gráf hozzáadása oldal](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. A **Gráf hozzáadása** oldalon adja meg az új gráf beállításait.

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Adatbázis-azonosító|sample-database|Az új adatbázisnak adja a *sample-database* nevet. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat `/ \ # ?` karaktereket vagy záró szóközt.
    Teljesítmény|400 kérelemegység|Módosítsa a teljesítményt másodpercenként 400 kérelemegységre (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.
    Gráfazonosító|sample-graph|Az új gyűjteménynek adja a *sample-graph* nevet. A gráfok nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázis-azonosítókra.
    Partíciókulcs| /pk |Minden Cosmos DB-fióknak szüksége van egy partíciókulcs vízszintesen skálázható. További információ a megfelelő partíciókulcs kiválasztásáról a [Graph Data Partitioning cikkben.](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning)

3. Az űrlap kitöltése után válassza az **OK gombot.**
