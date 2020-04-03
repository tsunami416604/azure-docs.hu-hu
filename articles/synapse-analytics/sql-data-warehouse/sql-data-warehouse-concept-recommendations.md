---
title: Szinapszis SQL-ajánlások
description: Ismerje meg a Synapse SQL-javaslatokat és azok létrehozásának módját
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c4bbd98cc28b242be5310fab76521a80de8fcb7c
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584128"
---
# <a name="synapse-sql-recommendations"></a>Szinapszis SQL-ajánlások

Ez a cikk ismerteti a Synapse SQL-javaslatok az Azure Advisoron keresztül kiszolgált.  

A Synapse SQL javaslatokat nyújt az adattárház-munkaterhelés teljesítményre való következetes optimalizálásához. A javaslatok szorosan integrálva vannak az [Azure Advisorral,](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) hogy közvetlenül az [Azure Portalon](https://aka.ms/Azureadvisor)keresztül nyújthassák önnek az ajánlott eljárásokat. A Synapse SQL telemetriai adatokat gyűjt, és javaslatokat tesz az aktív munkaterhelésnapi ütemben. A támogatott javaslati forgatókönyvek az alábbiakban ismertetjük az ajánlott műveletek alkalmazásának módját.

Akkor [ellenőrizze a javaslatokat](https://aka.ms/Azureadvisor) még ma! Jelenleg ez a funkció csak a Gen2 adatraktárakban alkalmazható. 

## <a name="data-skew"></a>Adatdöntés

Az adatdöntés további adatmozgást vagy erőforrás-szűk keresztmetszeteket okozhat a munkaterhelés futtatásakor. Az alábbi dokumentáció az optimális terjesztési kulcs kiválasztásával azonosítja az adatok elferdítését, és megakadályozza annak bekövetkezését.

- [Döntés azonosítása és eltávolítása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Nincs vagy elavult statisztika

Az optimálistól elmaradó statisztikák súlyosan befolyásolhatják a lekérdezés teljesítményét, mivel az SQL-lekérdezés-optimalizáló az optimálistól elmaradó lekérdezési tervek et hozhat létre. Az alábbi dokumentáció ismerteti a statisztikák létrehozásával és frissítésével kapcsolatos gyakorlati tanácsokat:

- [Táblastatisztika létrehozása és frissítése](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

A javaslatok által érintett táblák listájának megtekintéséhez futtassa a következő [T-SQL parancsfájlt.](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables) Az Advisor folyamatosan futtatja ugyanazt a T-SQL-parancsfájlt, hogy létrehozza ezeket a javaslatokat.

## <a name="replicate-tables"></a>Táblák replikálása

A replikált táblajavaslatok esetében az Advisor a következő fizikai jellemzők alapján észleli a táblajelölteket:

- Replikált tábla mérete
- Oszlopok száma
- Tábla eloszlástípusa
- Partíciók száma

Az Advisor folyamatosan kihasználja a számítási feladatokon alapuló heurisztikákat, például a tábla-hozzáférési gyakoriságot, az átlagosan visszaadott sorokat, valamint az adattárház mérete és tevékenységkörüli küszöbértékeket, hogy biztosítsa a kiváló minőségű javaslatok létrejöttét. 

Az alábbiakban ismertetjük a számítási feladatokon alapuló heurisztikát, amelyet az Azure Portalon találhat az egyes replikált táblajavaslatokhoz:

- Bekési átlag – az egyes táblázathozzáférések tábláihoz visszaadott sorok átlagos százaléka az elmúlt hét napban
- Gyakori olvasás, nincs frissítés - azt jelzi, hogy a tábla nem frissült az elmúlt hét napban, miközben hozzáférési tevékenységet mutatott
- Olvasási/frissítési arány - a táblázat elérésének aránya az elmúlt hét nap frissítéséhez képest
- Tevékenység – a használatot a hozzáférési tevékenység alapján méri. Ez összehasonlítja a tábla hozzáférési tevékenység képest az átlagos tábla hozzáférési tevékenység az adattárház az elmúlt hét nap. 

Jelenleg az Advisor egyszerre legfeljebb négy replikált táblajelöltet jelenít meg a legmagasabb tevékenységet rangsoroló fürtözött oszlopcentrikus indexekkel.

> [!IMPORTANT]
> A replikált tábla javaslat nem teljes bizonyíték, és nem veszi figyelembe az adatok mozgatási műveleteket. Dolgozunk hozzá ezt a heurisztikus, de addig is, mindig érvényesítse a számítási feladatok alkalmazása után az ajánlást. Lépjen sqldwadvisor@service.microsoft.com kapcsolatba, ha replikált táblajavaslatokat talál, amelyek a számítási feladatok visszalépést eredményeznek. Ha többet szeretne megtudni a replikált táblákról, látogasson el az alábbi [dokumentációba.](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table)
