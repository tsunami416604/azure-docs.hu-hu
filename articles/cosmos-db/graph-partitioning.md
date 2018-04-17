---
title: Graph API particionálás |} Microsoft Docs
description: Ismerje meg, hogyan használhatja a particionált diagramhoz az Azure Cosmos-Adatbázisba.
services: cosmos-db
author: luisbosquez
documentationcenter: ''
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: db41efeee467d2cda89f62e0a18cf89cec2d9e63
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>A particionált diagramhoz Azure Cosmos DB használatával

A Graph API-nak Azure Cosmos DB a kulcsfontosságú szolgáltatásokat egyike a nagy méretű diagramjait keresztül vízszintes méretezhetőség kezelésének képessége. Ez a folyamat a gazdafájlon keresztül, a [Azure Cosmos DB képességei particionálás](partition-data.md#how-does-partitioning-work), amelyek használja a gyűjtemények, más néven tárolók, tárolási és átviteli egymástól függetlenül is méretezhető. Azure Cosmos-adatbázis a következő típusú tárolók különböző minden API támogat:

- **Gyűjtemény rögzített**: ezekhez a gyűjteményekhez egy grafikonon tud tárolni legfeljebb 10 GB, legfeljebb 10 000 kérelemegység másodpercenként lefoglalt méret adatbázis. Rögzített méretű gyűjtemény létrehozásához meg kell határozni egy partíció-tulajdonsággal az adatok nem.

- **Korlátlan számú gyűjtemény**: ezekhez a gyűjteményekhez automatikusan át tudja méretezni meghaladja a 10 GB-os korlát keresztül vízszintes particionálás grafikon tárolásához. Mindegyik partíció fogja tárolni a 10 GB-os és az adatok lesz automatikusan kiegyensúlyozott alapján a **megadott partíciókulcs**, amely lesz egyik kötelező paraméter, ha egy korlátlan gyűjtemény használatával. Az ilyen típusú gyakorlatilag korlátlan adatméret tárolhatja, és engedélyezheti a legfeljebb 100 000 kérelemegység / másodpercben vagy több [lépjen kapcsolatba az ügyfélszolgálattal](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

Ebben a dokumentumban a részletekről a graph adatbázisok particionálásáról előnyben, annak leírását és a megvalósítását mindkét csúcsban (vagy csomópontok) és a.

## <a name="requirements-for-partitioned-graph"></a>A particionált graph követelményei

Az alábbiakban egy grafikonon particionált tároló létrehozásakor megértését igénylő részletek:
- **Particionálás beállítása szükség lesz** Ha a gyűjtemény várhatóan 10 GB-nál több és/vagy ha több mint 10 000 kérelemegység (RU/mp) másodpercenként lefoglalásával lesz szükség.
- **Mind a csúcsban, és a szélek JSON-dokumentumokként tárolja** a a háttér-Azure Cosmos DB Graph API-tároló.
- **Csúcsban igényelnek a partíciós kulcs**. Ez a kulcs határozza meg, melyik partíció a csúcspont tárolni fogja a kivonatolási algoritmust keresztül. A partíciós kulcs neve szóközt és speciális karakterek nélküli egy egyszavas karakterláncot, és azt történő létrehozásakor egy új gyűjteményt, a következő formátumban kell megadni `/partitioning-key-name` a portálon.
- **Szegélyek tárolja a rendszer a forrás csúcspont**. Ez azt jelenti az egyes csúcspont a partíciós kulcs határozza meg azokat a rendszer hol tárolja a kimenő szélei együtt. Ez annak elkerülése érdekében a kereszt-partíció lekérdezések használatakor történik a `out()` számossága graph lekérdezésekben.
- **Graph lekérdezések meg kell adnia egy partíciókulcsot**. Teljes mértékben történő kihasználásához esetén az Azure Cosmos Adatbázisba vízszintes particionálás, a partíciós kulcs meg kell adni, ha egyetlen csomópont van kiválasztva, ha lehetséges. Válasszon ki egy vagy több csúcsban particionált grafikon lekérdezéseket a következők:

    - Egy csúcsának azonosító szerint, majd kiválasztása **használatával a `.has()` lépéssel megadhatja azokat a partíciós kulcs tulajdonságát**: 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Által csúcspont kiválasztásával **egy rekord, beleértve a partíciós kulcs értéke és azonosító megadása**: 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Adja meg egy **rekordokat a partíciós kulcs értékeket és azonosítók tömbje**:
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Csúcsban készlete kiválasztása és **partíciókulcs-értékek listájának megadásával**: 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>A particionált diagramhoz használata esetén ajánlott eljárások

Útmutatást, amelyet a leghatékonyabb teljesítményét és méretezhetőségét biztosításához particionált diagramok a korlátlan gyűjtemények használata esetén a következők:
- **Mindig adja meg a partíciós kulcs értéke csúcspont lekérdezésekor**. A teljesítmény szempontjából lehető leghatékonyabb módon csúcspont beszerzése egy ismert partíció.
- **Használja a kimenő irányban szélek lekérdezésekor, ha lehetséges**. Fent említett szélek tárolja az adatforrás csúcsban a kimenő irányban. Ez azt jelenti, hogy az esélyét, végezhessenek kereszt-partícióra lekérdezések során az adatok és a lekérdezések tervezik ebben a mintában figyelembe másodpercekre csökken.
- **Válassza ki a partíciós kulcs, amely egyenlően osztható adatok el az közötti partíciók**. Ehhez a döntéshez fokozottan függ a megoldás az adatokat az adatmodellbe. További információk a egy megfelelő partíciós kulcs létrehozása [Partitining és a skála Azure Cosmos DB](partition-data.md).
- **Egy partíciót, amikor lehetséges határain belül adatok beszerzése a lekérdezések optimalizálását**. Az optimális particionálási stratégia lekérdező mintákat volna igazítva. Lekérdezéseiben, amelyeket egyetlen partíció a szerezhet a lehető legjobb teljesítményt nyújtanak.

## <a name="next-steps"></a>További lépések
Ez a cikk áttekintését fogalmak és ajánlott eljárások az Azure Cosmos DB Graph API-k a particionálás lett megadva. 

* További tudnivalók [partíció és a skála Azure Cosmos DB](partition-data.md).
* További tudnivalók a [Gremlin támogatása a Graph API](gremlin-support.md).
* További tudnivalók [Graph API bemutatása](graph-introduction.md).
