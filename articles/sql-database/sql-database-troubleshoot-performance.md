---
title: "Figyelési & teljesítményhangolás - Azure SQL Database |} Microsoft Docs"
description: "Tippek az teljesítményhangolás, az Azure SQL Database keresztül értékelése és javítása."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "SQL teljesítményének hangolása, adatbázis teljesítményének hangolása, ötleteket hangolás sql teljesítmény sql adatbázis teljesítményének hangolása"
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: v-shysun
ms.openlocfilehash: 4eb344bf9e87ef1a8f469268211588e51696164e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="monitoring-and-performance-tuning"></a>Figyelés és teljesítményének hangolása

Az Azure SQL Database automatikusan kezeli, és rugalmas adatszolgáltatás, ahol egyszerűen megfigyeléséhez, vegye fel vagy távolítsa el az erőforrások (Processzor, memória, io), található, amely az adatbázis teljesítményének növelése, vagy lehetővé teszik a munkaterhelés igazítja adatbázis javaslatok és automatikusan teljesítményének optimalizálásához.

Ez a cikk áttekintést nyújt a figyelési és teljesítményének hangolása lehetőségek állnak rendelkezésre az Azure SQL-adatbázis.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Figyelés és hibaelhárítás céljából az adatbázis teljesítménye

Az Azure SQL Database lehetővé teszi, hogy egyszerűen figyelheti az adatbázis-használat, és azonosíthatja a lekérdezések, amelyek a teljesítménnyel kapcsolatos problémákat okozhatnak. Adatbázis teljesítménye az Azure portál vagy a rendszer nézetek használatával figyelheti. Lehetősége van a következő figyelés és hibaelhárítás céljából az adatbázis teljesítményét:

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **SQL-adatbázisok**, és válassza ki az adatbázist, majd használja a figyelés diagramra hamarosan eléri a maximális erőforrások kereséséhez. DTU-használat alapértelmezés szerint megjelenik. Kattintson a **szerkesztése** időtartomány és látható értékek módosításához.
2. Használjon [lekérdezési Terheléselemző](sql-database-query-performance.md) azonosítására, hogy a legtöbb az erőforrások töltött lekérdezéseket.
3. Dinamikus felügyeleti nézetek (dinamikus felügyeleti nézetek) bővített eseményektől (`XEvents`), és a Lekérdezéstár szolgáltatáshoz az ssms valós idejű teljesítményparaméterek eléréséhez.

Tekintse meg a [teljesítmény útmutató témakör](sql-database-performance-guidance.md) használó Azure SQL adatbázis teljesítményének növelése, ha néhány problémát, használja a következő jelentéseket, illetve nézetek azonosítani kereséséhez.

> [!IMPORTANT] 
> Javasoljuk, hogy mindig a Management Studio legfrissebb verzióját használja, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optimalizálása teljesítményének javítása érdekében

Az Azure SQL Database lehetővé teszi, hogy azonosítsa a finomhangolási lehetőségeket javítása és a lekérdezési teljesítmény optimalizálása érdekében erőforrások megtekintésével módosítása nélkül [teljesítményének hangolása javaslatok](sql-database-advisor.md). A hiányzó indexek és rosszul optimalizált lekérdezések az adatbázis gyenge teljesítményének gyakori okai. Ezek a számítási feladatok teljesítményének javítása érdekében a hangolási javaslatok is alkalmazhatja.
Is lehetővé Azure SQL-adatbázis [automatikusan a a lekérdezések teljesítményének optimalizálásához](sql-database-automatic-tuning.md) alkalmazásával az összes azonosított javaslatok és, hogy azokat teljesítménynövelés adatbázis ellenőrzése. Az adatbázis teljesítményének javításával használhatja a következő beállításokat:

1. Használjon [SQL Database Advisor](sql-database-advisor-portal.md) létrehozása és az indexek eldobását, a lekérdezések paraméterezése és a séma problémák kijavítása javaslatok megtekintéséhez.
2. [Engedélyezze az automatikus hangolással](sql-database-automatic-tuning-enable.md) és lehetővé teszik az Azure SQL adatbázis automatikus javítás azonosított teljesítményproblémákat.

## <a name="improving-database-performance-with-more-resources"></a>További erőforrások az adatbázis teljesítményének növelése

Végezetül végrehajtható elem, amely az adatbázis teljesítményének esetén módosíthatja az Azure SQL-adatbázis elérhető erőforrások mennyisége. További erőforrások hozzárendeléséhez módosítása a [szolgáltatásréteg](sql-database-service-tiers.md) egy önálló adatbázis vagy a bármikor a rugalmas készlet edtu-inak növelését.
1. Az önálló adatbázisok esetén is [szolgáltatásszintek módosítása](sql-database-service-tiers.md) igény szerinti adatbázis teljesítményének javítása érdekében.
2. Több adatbázis esetén érdemes [rugalmas készletek](sql-database-elastic-pool-guidance.md) erőforrások automatikus méretezése.

## <a name="tune-and-refactor-application-or-database-code"></a>Finomhangolja és azonosítóterületen alkalmazás vagy adatbázis-kód

Módosíthatja az alkalmazás kódjában, hogy több optimálisan használja a-adatbázist, indexek módosítása, tervek kényszerített vagy mutatók segítségével manuálisan alkalmazkodnak az adatbázis, a munkaterhelés. Néhány útmutatás és tippek található manuális beállítás, és írja át a kódot a [teljesítmény útmutató témakör](sql-database-performance-guidance.md) cikk.


## <a name="next-steps"></a>Következő lépések

- Engedélyezze az automatikus hangolással az Azure SQL Database és az automatikus, teljes mértékben felügyelhesse a számítási feladatok hangolási szolgáltatás segítségével [engedélyezze az automatikus hangolással](sql-database-automatic-tuning-enable.md).
- Manuális hangolása használatához tekintse át [javaslatok az Azure-portálon hangolása](sql-database-advisor-portal.md) és alkalmazza manuálisan a gazdarendszerhez a lekérdezések teljesítményének javítása.
- Ha megváltoztatja az adatbázis rendelkezésre álló erőforrások módosítása [Azure SQL Database szolgáltatási szinteket](sql-database-performance-guidance.md)
