---
title: "Az Azure SQL Database - automatikus hangolása |} Microsoft Docs"
description: "Az Azure SQL-adatbázis SQL-lekérdezés elemzi, és automatikusan alkalmazkodik felhasználói munkaterhelés."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: drasumic
editor: danimir
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 11/08/2017
ms.author: jovanpop
ms.openlocfilehash: 34aa035368a0516f9fa1c71c1bda491daf5ab370
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Az Azure SQL-adatbázis automatikus hangolása

Az Azure SQL adatbázis automatikus hangolása csúcsteljesítmény és stabil munkaterhelések mesterséges Eszközintelligencia használata folyamatos teljesítményhangolás keresztül biztosít.

Automatikus hangolása egy teljes körűen felügyelt szolgáltatás, amely folyamatosan figyelni az adatbázison végrehajtott lekérdezések beépített funkciói segítségével, és automatikusan javítja a teljesítményt. Ez dinamikusan testreszabásához az adatbázis és a munkaterhelések módosítása hangolási javaslatok alkalmazása keresztül érhető el. Az Azure keresztül mesterséges Eszközintelligencia lévő összes adatbázis vízszintesen automatikus hangolása megtanulja, és dinamikusan javítja a hangolási tevékenységeit. Minél hosszabb egy Azure SQL Database futtatja az automatikus hangolással, annál jobban hajt végre.

Az Azure SQL adatbázis automatikus hangolása lehet egyik legfontosabb szolgáltatás stabil és a munkaterhelések végrehajtása csúcs engedélyezhető.

## <a name="what-can-automatic-tuning-do-for-you"></a>Mi az Ön automatikus hangolása elvégezni?

- Automatizált teljesítményhangolás Azure SQL-adatbázisok
- Az teljesítménynövekedéshez automatizált ellenőrzése
- Automatikus visszaállítás és önálló javítása
- Naplózási előzmények hangolása
- T-SQL-parancsfájlok művelet manuális telepítésekhez hangolása
- Proaktív munkaterhelés-figyelő
- Horizontális felskálázás képességet a több száz akár több ezer adatbázis
- A DevOps erőforrásokhoz és a teljes birtoklási költség pozitív hatása

## <a name="safe-reliable-and-proven"></a>Biztonságos, megbízható és bevált

Azure SQL-adatbázisok alkalmazott hangolási műveletek teljes biztonságosak a legtöbb kiemelt alkalmazások teljesítményét. A rendszer tervezték körültekintően nem zavarja a felhasználói feladatait. Automatikus hangolási javaslatok csak az alacsony kihasználtságú időpontokban érvényesek. A rendszer is ideiglenesen letilthatja az automatikus hangolási műveletek védelme érdekében a számítási feladat teljesítményére. Ilyen esetben "Letiltott a rendszer" üzenet jelenik meg az Azure portálon. Az automatikus hangolással a legmagasabb prioritású erőforrás munkaterhelések tekintetében.

Automatikus hangolási módszerek érett, és akár több ezer adatbázis Azure-on futó százait rendelkezik lett perfected. Automatizált hangolási műveletek alkalmazása automatikusan ellenőrzése a gondoskodjon arról, hogy egy pozitív fejlesztésének a számítási feladat teljesítményére. Teljesítmény közleményében szerepelt javaslatok dinamikusan észlelt, és azonnal vissza. A hangolási Előzmények napló keresztül van minden Azure SQL Database javulás hangolása egyértelmű nyomkövetési. 

![Hogyan működik az automatikus hangolási munkahelyi](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Az Azure SQL adatbázis automatikus hangolása az SQL Server automatikus hangolási motor megosztja az alapvető logikát. A beépített eszközintelligencia mechanizmus a további technikai információk: [SQL Server automatikus hangolása](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Használja az automatikus hangolása

Automatikus hangolása kell saját kezűleg engedélyezni kell az előfizetéshez. Azure-portál használatával automatikus hangolása engedélyezéséről [engedélyezze az automatikus hangolással](sql-database-automatic-tuning-enable.md).

Automatikus hangolása is önállóan keresztül automatikusan alkalmazza az hangolási ajánlásokat, beleértve az teljesítménynövekedéshez automatizált ellenőrzése. 

Jobban kézben hangolása javaslatok automatikus alkalmazása is ki kell kapcsolni, és javaslatok hangolása manuálisan alkalmazhatja az Azure portálon keresztül. Akkor is megoldást kíván használni a hangolási javaslatok automatikus megtekintéséhez, és manuálisan alkalmazhatja azokat a parancsfájlok és eszközök az Ön által választott keresztül. 

Megtudhatja, hogyan automatikus hangolási működik, és a jellemző használati forgatókönyvei tekintse meg a beágyazott videót:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Automatikus hangolási lehetőségeket

Elérhető az Azure SQL-adatbázis automatikus hangolási lehetőségek a következők:
 1. **A CREATE INDEX** , amely azonosítja az indexeket, javíthatja a teljesítményt a munkaterhelés, az indexek létrehozása, amely ellenőrzi, hogy azok a lekérdezések teljesítményének javítása.
 2. **DROP INDEX** , amely azonosítja a redundancia és ismétlődő indexek és indexek, amelyet nem használtak a hosszú idő alatt.
 3. **UTOLSÓ JÓ terv KÉNYSZERÍTETT** , amely az SQL-lekérdezések által használt végrehajtási terv, amik lassabbak, mint az előző jó terv azonosítja, és az utolsó ismert helyes terv használ, a regressed terv nem.

Az Azure SQL Database azonosítja **a CREATE INDEX**, **DROP INDEX**, és **KÉNYSZERÍTETT utolsó JÓ terv** is optimalizálhatja az adatbázist, és az Azure portálon megjelenő javaslatokat. A módosítani kívánt indexek azonosítása további információt talál [indexjavaslatok az Azure portálon található](sql-database-advisor-portal.md). Manuálisan alkalmazhatja javaslatok a portál használatával, vagy lehetővé teszik az Azure SQL Database automatikusan javaslatok alkalmazásához számítási feladat figyeléséhez a módosítás után, és győződjön meg arról, hogy a javaslat javult a számítási feladatok teljesítményére.

Automatikus hangolása beállítások egymástól függetlenül kapcsolható be- és kikapcsolása adatbázisonként, vagy logikai kiszolgálón konfigurált és az összes adatbázisra, amelyet a örököl beállításokat a kiszolgálón alkalmazott. Automatikus hangolása beállítások a kiszolgálón és a beállítások a kiszolgálón az adatbázisok öröklődés konfigurálása konfigurálása automatikus hangolása, mert a nagy számú adatbázis automatikus hangolási lehetőségeket kezelését megkönnyíti a módszer használata ajánlott.

## <a name="next-steps"></a>Következő lépések

- Az Azure SQL Database a számítási feladatok kezelésére automatikus hangolása engedélyezéséről [engedélyezze az automatikus hangolással](sql-database-automatic-tuning-enable.md).
- Manuálisan tekintse át és az automatikus hangolással javaslatok alkalmazása [keresse meg és teljesítmény javaslatok alkalmazása](sql-database-advisor-portal.md).
- Automatikus hangolása használt beépített eszközintelligencia kapcsolatos további információkért lásd: [mesterséges Eszközintelligencia beállítja az Azure SQL-adatbázisok](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Az Azure SQL Database és SQL server 2017 automatikus hangolási works kapcsolatos további információkért lásd: [SQL Server automatikus hangolása](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning).
