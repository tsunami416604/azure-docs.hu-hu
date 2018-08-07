---
title: Az Azure SQL Database – automatikus finomhangolási |} A Microsoft Docs
description: Az Azure SQL Database elemzi az SQL-lekérdezést, és automatikusan alkalmazkodik felhasználómennyiség kiszolgálására alkalmasak.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 6ed113d11a0c61620ae4a9d48eee957bb979dc11
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526793"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Az Azure SQL Database automatikus finomhangolása

Az Azure SQL Database automatikus finomhangolása maximális teljesítménnyel és a stabil számítási feladatokat, mesterséges intelligenciát használó folyamatos teljesítményhangolás keresztül biztosít.

Az automatikus hangolás egy teljes körűen felügyelt szolgáltatás, amely a beépített intelligenciával folyamatosan figyelni az adatbázison végrehajtott lekérdezések használ, és automatikusan javítja a teljesítményüket. Ez dinamikusan gyakorlatainak az adatbázist, hogy a számítási feladatok módosítása és a hangolási ajánlásokat alkalmazása keresztül érhető el. Az Azure-on keresztül mesterséges intelligenciát használó összes adatbázis automatikus finomhangolási vízszintesen tanul, és ez dinamikusan növeli a hangolási műveletek. Minél tovább fut, az Azure SQL Database automatikus finomhangolása a, minél jobban hajt végre.

Az Azure SQL Database automatikus finomhangolása lehet egyik legfontosabb funkciója, amely stabil és számítási feladatok számára engedélyezheti.

## <a name="what-can-automatic-tuning-do-for-you"></a>Mi mindent az automatikus hangolás az Ön számára?

- Az Azure SQL Database-adatbázisok az automatikus teljesítményhangolás
- Teljesítményre képes automatikus ellenőrzése
- Automatikus visszaállítás és önkiszolgáló javítása
- Hangolási Előzmények napló
- Manuális telepítés a művelet a T-SQL parancsfájl-finomhangolás
- Proaktív munkaterhelés alkalmazásteljesítmény-figyelés
- Horizontális felskálázás funkció a több százezer adatbázis
- Pozitív hatással lehet a DevOps-erőforrások és a teljes birtoklási költség

## <a name="safe-reliable-and-proven"></a>Biztonságos, megbízható és bevált

Az Azure SQL Database-adatbázisokhoz a alkalmazni hangolási műveletek teljes biztonságosak a legnagyobb igényű számítási feladatok teljesítményére. A rendszer, nem zavarja meg a felhasználói feladatok úgy lett kialakítva körültekintően. Az automatikus finomhangolási ajánlásait csak egy alacsony kihasználtság rendszerideje vonatkoznak. A rendszer ideiglenesen letilthatja az automatikus finomhangolási műveletek védelme érdekében a számítási feladatok teljesítményére. Ebben az esetben "A rendszer által letiltva" üzenet jelenik meg az Azure Portalon. Az automatikus hangolás tekintetében a legmagasabb prioritású erőforrás számítási feladatokhoz.

Az automatikus finomhangolási mechanizmusok érett, és rendelkezik lett kedve szerint a több százezer Azure-on futó adatbázisok. Automatikus hangolási műveletek alkalmazása automatikusan ellenőrzése annak érdekében, hogy van egy pozitív fokozása a számítási feladatok teljesítményére. Romlott teljesítményű javaslatok dinamikusan észlelt, és azonnal vissza. Keresztül a hangolási Előzmények napló nincs egyértelmű nyomkövetési finomhangolási fejlesztések végrehajtott minden egyes Azure SQL Database-adatbázishoz. 

![Működése automatikus finomhangolási](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Az Azure SQL Database automatikus finomhangolása az SQL Server automatikus finomhangolási motor megosztást az alapvető logikát. A beépített intelligenciával mechanizmus a további technikai információk: [SQL Server automatikus finomhangolási](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Használja az automatikus hangolás

Az automatikus hangolás az előfizetéshez, manuálisan engedélyeznie kell. Ha engedélyezni szeretné az automatikus hangolás az Azure portal használatával, lásd: [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md).

Az automatikus hangolás is üzemelnek automatikusan alkalmazza a finomhangolási ajánlásait, beleértve az automatizált ellenőrzése teljesítménynövekedést keresztül. 

Több vezérlő esetén is ki van kapcsolva az automatikus finomhangolási javaslatai alkalmazása, és finomhangolási javaslatai manuálisan alkalmazhatja az Azure Portalon keresztül. Akkor is használhatja a megoldást csak az automatikus finomhangolási ajánlásait megtekintése, majd manuálisan alkalmazhatja ezeket a parancsfájlokat és eszközöket a választott keresztül. 

Automatikus hangolási works áttekintését és a jellemző használati forgatókönyvei tekintse meg a beágyazott videót:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Az automatikus finomhangolási beállítások

Az Azure SQL Database automatikus finomhangolási beállítások a következők:
 1. **A CREATE INDEX** -indexek, amely növelheti a számítási feladatok teljesítményére, indexeli, és automatikusan ellenőrzi, hogy a lekérdezések teljesítménye javult azonosítja.
 2. **DROP INDEX** -redundáns és ismétlődő indexeket és az indexek, amelyet nem használtak egy nagyon hosszú ideig azonosítja. Vegye figyelembe, hogy ezt a beállítást, nem kompatibilis alkalmazások használatával a partíció közötti váltás és index mutatókat.
 3. **FORCE LAST GOOD Plan csomag** -SQL-lekérdezések végrehajtási terv, amely alacsonyabb, mint az előző jó terv, és lekérdezi az utolsó ismert jó terv helyett a romlott terv használatával azonosítja.

Az Azure SQL Database azonosítja **a CREATE INDEX**, **DROP INDEX**, és **KÉNYSZERÍTETT utolsó JÓ terv** javaslatok, amelyek optimalizálhatja az adatbázis, és megjeleníti őket az Azure Portalon. További információ a módosítani kívánt indexek azonosítása [ajánlott indexek az Azure portálon található](sql-database-advisor-portal.md). Manuálisan alkalmazhatja a javaslatok a portál használatával, vagy lehetővé teszik az Azure SQL Database automatikusan alkalmazza a javaslatokat, számítási feladat monitorozása a váltás után, és győződjön meg arról, hogy a javaslat javítani a számítási feladatok teljesítményére. 

Az automatikus finomhangolási beállításokat is egymástól függetlenül engedélyezhető vagy letiltható adatbázisonként, vagy logikai kiszolgálón konfigurálva és minden adatbázis, amely örökli a beállításokat a kiszolgálóról a alkalmazni. Logikai kiszolgáló automatikus hangolási beállításai az Azure alapértelmezett is öröklik. Jelenleg az Azure alapértelmezés szerint engedélyezve van a FORCE_LAST_GOOD_PLAN, CREATE_INDEX engedélyezve van, és DROP_INDEX le van tiltva.

Automatikus finomhangolási beállításokat a kiszolgálón, és örökli a beállításokat a fölérendelt kiszolgálóhoz tartozó adatbázisok konfigurálása a konfigurálásához az automatikus hangolás, mert egyszerűbbé teszi az automatikus finomhangolási beállítások nagy számú adatbázishoz felügyeleti javasolt módszer.

## <a name="next-steps"></a>További lépések

- Engedélyezheti a a számítási feladatok kezelése Azure SQL Database automatikus finomhangolása [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md).
- Manuálisan ellenőrizze és az automatikus finomhangolási javaslatai vonatkoznak [keresse meg és teljesítménnyel kapcsolatos javaslatok alkalmazása](sql-database-advisor-portal.md).
- E-mail értesítések automatikus finomhangolási ajánlásait létrehozásával kapcsolatos tudnivalókért lásd: [E-mail-értesítések automatikus finomhangolási](sql-database-automatic-tuning-email-notifications.md)
- Az automatikus hangolás használt beépített intelligenciával kapcsolatos további információkért lásd: [mesterséges intelligencia hangolja az Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Az Azure SQL Database és az SQL server 2017 automatikus finomhangolási működésével kapcsolatos további információkért lásd: [SQL Server automatikus finomhangolási](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
