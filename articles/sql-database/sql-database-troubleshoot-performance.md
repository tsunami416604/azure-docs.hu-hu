---
title: Monitorozás és teljesítményhangolás – Azure SQL Database |} A Microsoft Docs
description: Tippek a teljesítmény-finomhangolási az Azure SQL Database révén értékelése és javítása.
services: sql-database
author: danimir
manager: craigg
editor: ''
keywords: teljesítmény-finomhangolási, adatbázis teljesítmény-finomhangolási, sql teljesítmény-finomhangolási tippek SQL az sql database teljesítményének hangolása
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 8d8b983a5304f227d09392198d0f1307fe6dafde
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524184"
---
# <a name="monitoring-and-performance-tuning"></a>Figyelés és a teljesítmény hangolása

Az Azure SQL Database automatikusan kezeli, és a szolgáltatás rugalmas, ahol Ön is egyszerűen figyelheti, erőforrásainak hozzáadása vagy eltávolítása (CPU, memória, i/o), keresse meg a javaslatok, amelyek javítani az adatbázis teljesítményét, vagy lehetővé teszik az adatbázis alkalmazkodik számítási feladatához és automatikusan optimalizálhatja a teljesítményt.

Ez a cikk áttekintést nyújt az figyelési és teljesítményhangolási lehetőségekről az Azure SQL Database által biztosított.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Megfigyelés és hibaelhárítás az adatbázis-teljesítmény

Az Azure SQL Database lehetővé teszi, hogy egyszerűen monitorozhatja az adatbázis-használat, és azonosíthatja a lekérdezések, amelyek teljesítményproblémákat okozhatnak. Adatbázis teljesítménye az Azure portal vagy a rendszer nézetek használatával követheti nyomon. A megfigyelés és hibaelhárítás az adatbázis teljesítményét a következő lehetőségek állnak rendelkezésére:

1. Az a [az Azure portal](https://portal.azure.com), kattintson a **SQL-adatbázisok**, és válassza ki az adatbázist, majd használja a figyelés diagramra a hamarosan eléri a maximális erőforrások kereséséhez. DTU-fogyasztásának alapértelmezés szerint jelenik meg. Kattintson a **szerkesztése** módosíthatja az időtartományt és látható értékeket.
2. Használat [lekérdezési Terheléselemző](sql-database-query-performance.md) azonosítására, hogy a legtöbbet az erőforrások költségek lekérdezéseket.
3. Használhatja a dinamikus felügyeleti nézetekkel (DMV-kkel), bővített események (`XEvents`), és a Query Store az ssms-ben a valós idejű teljesítmény paraméterek kaphat.

Tekintse meg a [teljesítmény-útmutató témakör](sql-database-performance-guidance.md) módszereket, amelyek segítségével az Azure SQL Database teljesítményének növelése, ha néhány problémát, ezek a jelentések vagy nézetek segítségével azonosítani tudja.

> [!IMPORTANT] 
> Javasoljuk, hogy mindig a Management Studio legfrissebb verzióját használja, hogy kihasználhassa a Microsoft Azure és az SQL Database legújabb frissítései által nyújtott előnyöket. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optimalizálja teljesítményének javítása érdekében

Az Azure SQL Database lehetővé teszi, azokat a lehetőségeket és a lekérdezési teljesítmény optimalizálása áttekintésével erőforrások módosítása nélkül [teljesítmény-finomhangolási ajánlásait](sql-database-advisor.md). A hiányzó indexek és rosszul optimalizált lekérdezések az adatbázis gyenge teljesítményének gyakori okai. Ezeket a számítási feladat teljesítményét a hangolási javaslatokat is alkalmazható.
Is tegye lehetővé, Azure SQL database [automatikus optimalizálása a lekérdezések teljesítményének](sql-database-automatic-tuning.md) alkalmazásával az összes azonosított javaslatok és adatbázis-teljesítmény javítása ellenőrzése. A következő beállítások segítségével javíthatja az adatbázis teljesítményét:

1. Használat [az SQL Database Advisor](sql-database-advisor-portal.md) létrehozása és az indexek elvetését, a lekérdezések paraméterezése és a séma problémáinak javítása javaslatok megtekintése.
2. [Automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md) , és lehetővé teszik az Azure SQL database automatikusan javítja az azonosított teljesítménybeli problémák.

## <a name="improving-database-performance-with-more-resources"></a>További erőforrások az adatbázis teljesítményének növelése

Végül ha nem találhatók gyakorlatban hasznosítható elemek, amelyek javíthatja az adatbázis teljesítményét, módosíthatja az Azure SQL Database-ben rendelkezésre álló erőforrások mennyiségét. Hozzárendelhet további erőforrások módosításával a [szolgáltatási szint dtu-k](sql-database-service-tiers-dtu.md) egy önálló adatbázis vagy növelje az egy rugalmas készlet edtu-k tetszőleges időpontban. Azt is megteheti Ha használja a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md), módosíthatja a szolgáltatási rétegben, vagy növelje az adatbázis számára elkülönített erőforrások. 
1. Az önálló adatbázisokhoz is [szolgáltatásszintek módosítása](sql-database-service-tiers-dtu.md) vagy [számítási erőforrások](sql-database-service-tiers-vcore.md)igény szerinti adatbázis-teljesítmény javítása érdekében.
2. Több adatbázis esetén fontolja meg [rugalmas készletek](sql-database-elastic-pool-guidance.md) erőforrások automatikus méretezése.

## <a name="tune-and-refactor-application-or-database-code"></a>Hangolás és újrabontása alkalmazás vagy adatbázis-kód

Módosíthatja az alkalmazás kódjában, hogy több optimálisan az adatbázist, módosítsa az indexek, tervek kényszerített vagy mutatók segítségével manuálisan alkalmazkodik a számítási feladatok az adatbázis. Keresse meg az egyes tanácsokat és javaslatokat manuális finomhangolásához és a kód újraírását a [teljesítmény-útmutató témakör](sql-database-performance-guidance.md) cikk.


## <a name="next-steps"></a>További lépések

- Engedélyezze az Azure SQL Database automatikus finomhangolása és lehetővé teszik a számítási feladat teljes mértékben kezelése, automatikus finomhangolási funkció: [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md).
- Manuális hangolás használatához áttekintheti [finomhangolási javaslatai az Azure Portalon](sql-database-advisor-portal.md) , és manuálisan a alkalmazni azokat, amelyek javítják a lekérdezések teljesítményét.
- Módosítsa úgy módosítja az adatbázist a rendelkezésre álló erőforrások [Azure SQL Database-szolgáltatásszintek](sql-database-performance-guidance.md)
