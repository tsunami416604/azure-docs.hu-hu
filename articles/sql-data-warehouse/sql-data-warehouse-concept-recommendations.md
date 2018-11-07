---
title: SQL Data Warehouse-ajánlatok – fogalmak |} A Microsoft Docs
description: Ismerje meg az SQL Data Warehouse javaslatok, és hogyan generáljon
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 712eed36f3a68ee02668849207835e3c8bdb8238
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232154"
---
# <a name="sql-data-warehouse-recommendations"></a>Az SQL Data Warehouse javaslatok

Ez a cikk ismerteti az Azure advisorral az SQL Data Warehouse által kiszolgált javaslatokat.  

Az SQL Data Warehouse biztosít annak biztosítása érdekében az adattárház javaslatok következetesen teljesítmény van optimalizálva. Data warehouse javaslatok szorosan integrálva [az Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) belül ajánlott eljárások közvetlenül tudja biztosítani a [az Azure portal](https://aka.ms/Azureadvisor). Az SQL Data Warehouse elemzi az adattárház jelenlegi állapotát, a napi szintű telemetriai és a Surface-eszközök javaslatok a aktív számítási feladatok számára gyűjti. Az alábbiakban javasolt műveletek alkalmazása mellett vázolt a támogatott data warehouse javaslat forgatókönyveket.

Ha bármilyen visszajelzése van az SQL Data Warehouse Advisor a vagy problémákat tapasztal, forduljon [ sqldwadvisor@service.microsoft.com ](mailto:sqldwadvisor@service.microsoft.com).   

Kattintson a [Itt](https://aka.ms/Azureadvisor) a javaslatok ellenőrzése még ma! Ez a funkció jelenleg csak a Gen2 adattárházak alkalmazható. 

## <a name="data-skew"></a>Adateltérés

Adatok torzulása további adatok mozgását vagy az erőforrás szűk keresztmetszeteket okozhat, a számítási feladatok futtatásakor. Az alábbi dokumentáció azt ismerteti, adatok torzulása azonosításához, és a egy optimális terjesztési kulcs kiválasztásával el kerülni megjelenítése.

- [Azonosításához és eltávolításához a döntés](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Nem vagy elavult statisztika

Optimálisnál rosszabb statisztikáit súlyosan hatással lehet a lekérdezési teljesítmény, az optimálisnál rosszabb lekérdezési tervek készítése az SQL Data Warehouse lekérdezésoptimalizáló okozhat. Az alábbi dokumentáció azt ismerteti, hogy körül statisztikák létrehozása és frissítése az ajánlott eljárásokat:

- [Tábla statisztikák létrehozása és frissítése](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Ezekkel az ajánlásokkal által érintett táblák listájának megtekintéséhez futtassa a következő [T-SQL parancsfájl](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Az Advisor folyamatosan futó ugyanezt a T-SQL szkriptet létrehozni ezeket a javaslatokat.

## <a name="replicate-tables"></a>Táblák replikálása

Az Advisor replikált tábla javaslatok, észleli a tábla jelöltek a következő fizikai jellemzők alapján:

- Replikált tábla mérete
- Oszlopok száma
- Tábla terjesztési típusa
- A partíciók száma

Az Advisor folyamatosan kihasználja az heurisztika terhelésen alapuló tábla hozzáférés gyakorisága, például a visszaadott sorok átlagosan és küszöbértékek adatok adatraktár-méretét és magas színvonalú javaslatok jönnek létre, hogy a tevékenység. 

A következő előfordulhat, hogy az Azure Portalon minden replikált tábla ajánlási terhelésen alapuló heurisztika ismerteti:

- Átlagos – a átlagos százalékos aránya az elmúlt hét napban a tábla minden egyes tábla hozzáférés visszaadott sorok beolvasása
- Gyakori Olvasás, frissítés nélkül – azt jelzi, hogy a tábla nem lett frissítve az elmúlt hét napban hozzáférési tevékenység megjelenítése közben a
- Olvasási és frissítési arány – hogyan gyakori a tábla fértek hozzá mikor frissül az elmúlt hét napban viszonyított aránya
- Tevékenység – a használati hozzáférési tevékenység alapján méri. A tábla hozzáférési tevékenység az átlagos tábla-hozzáférési tevékenységet képest ez összehasonlítja az adatraktár között az elmúlt hét napban. 

Jelenleg az Advisor csak akkor jelenik meg, legfeljebb négy replikált tábla jelöltek egyszerre fontossági sorrend a legnagyobb tevékenység fürtözött oszlopcentrikus indexekkel rendelkező.

> [!IMPORTANT]
> A replikált tábla javaslat nem teljes koncepció, és nem veszik figyelembe az adatátviteli műveletek. Ez egy heurisztika hozzáadása folyamatban van, de addig is kell mindig ellenőrizze, hogy a számítási feladatok a javaslat alkalmazása után. Vegye fel a kapcsolatot sqldwadvisor@service.microsoft.com Ha a tudomására jut, amelynek hatására a számítási feladatok, amelyikre a replikált tábla javaslatok. Replikált táblák kapcsolatos további információkért látogasson el a következő [dokumentáció](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
>
