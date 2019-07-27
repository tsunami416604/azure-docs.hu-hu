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
ms.openlocfilehash: a24159a482c55496bb1e6031be951196258a6796
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565463"
---
Az Azure Portal Adatkezelő eszközét mostantól gráfadatbázisok létrehozására is használhatja. 

1. Kattintson az **Adatkezelő** > **Új gráf** lehetőségre.

    A jobb szélen megjelenik a **Gráf hozzáadása** terület. Előfordulhat, hogy jobbra kell görgetnie, hogy lássa.

    ![Az Azure Portal Adatkezelő, Gráf hozzáadása oldal](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. A **Gráf hozzáadása** oldalon adja meg az új gráf beállításait.

    Beállítás|Ajánlott érték|Leírás
    ---|---|---
    Adatbázis-azonosító|sample-database|Az új adatbázisnak adja a *sample-database* nevet. Az adatbázis neve 1–255 karakter hosszúságú lehet, és nem tartalmazhat `/ \ # ?` karaktereket vagy záró szóközt.
    Teljesítmény|400 kérelemegység|Módosítsa a teljesítményt másodpercenként 400 kérelemegységre (RU/s). Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.
    Gráfazonosító|sample-graph|Az új gyűjteménynek adja a *sample-graph* nevet. A gráfok nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázis-azonosítókra.
    Partíciókulcs| /pk |Az összes Cosmos DB-fióknak szüksége van egy partíciós kulcsra a horizontális skálázáshoz. Megtudhatja, hogyan választhatja ki a megfelelő partíciós kulcsot a [Graph Adatparticionálási cikkben](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

3. Miután kitöltötte az űrlapot, kattintson az **OK** elemre.
