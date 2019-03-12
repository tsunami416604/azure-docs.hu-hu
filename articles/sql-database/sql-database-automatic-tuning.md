---
title: Az Azure SQL Database – automatikus finomhangolási |} A Microsoft Docs
description: Az Azure SQL Database elemzi az SQL-lekérdezést, és automatikusan alkalmazkodik felhasználómennyiség kiszolgálására alkalmasak.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: e872c29712c3fadca676ec87870bcc5c4eb58565
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727399"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Az Azure SQL Database automatikus finomhangolása

Az Azure SQL Database automatikus finomhangolása biztosítja a maximális teljesítmény- és stabil számítási feladatokat, folyamatos teljesítményhangolás alapuló mesterséges Intelligencia és gépi tanulás útján.

Az automatikus hangolás beépített intelligenciával folyamatosan figyelni az adatbázison végrehajtott lekérdezések használó intelligens teljesítmény teljes körűen felügyelt szolgáltatás, és automatikusan javítja a teljesítményüket. Ez dinamikusan gyakorlatainak az adatbázist, hogy a számítási feladatok módosítása és a hangolási ajánlásokat alkalmazása keresztül érhető el. Azure AI-t az összes adatbázis automatikus finomhangolási vízszintesen tanul, és ez dinamikusan növeli a hangolási műveletek. Minél tovább fut, az Azure SQL Database automatikus finomhangolása a, minél jobban hajt végre.

Az Azure SQL Database automatikus finomhangolása lehet egyik legfontosabb funkciója, amely stabil és adatbázis-munkaterhelés számára engedélyezheti.

## <a name="what-can-automatic-tuning-do-for-you"></a>Mi mindent az automatikus hangolás az Ön számára?

- Az Azure SQL Database-adatbázisok az automatikus teljesítményhangolás
- Teljesítményre képes automatikus ellenőrzése
- Automatikus visszaállítás és önkiszolgáló javítása
- Hangolási előzmények
- Manuális telepítés a művelet a T-SQL parancsfájl-finomhangolás
- Proaktív munkaterhelés alkalmazásteljesítmény-figyelés
- Horizontális felskálázás funkció a több százezer adatbázis
- Pozitív hatással lehet a DevOps-erőforrások és a teljes birtoklási költség

## <a name="safe-reliable-and-proven"></a>Biztonságos, megbízható és bevált

Finomhangolási műveleteket a alkalmazni az Azure SQL-adatbázisok teljes biztonságban a legnagyobb igényű számítási feladatok ellátásához. A rendszer, nem zavarja meg a felhasználói feladatok úgy lett kialakítva körültekintően. Az automatikus finomhangolási ajánlásait csak egy alacsony kihasználtság rendszerideje vonatkoznak. A rendszer ideiglenesen letilthatja az automatikus finomhangolási műveletek védelme érdekében a számítási feladatok teljesítményére. Ebben az esetben "A rendszer által letiltva" üzenet jelenik meg az Azure Portalon. Az automatikus hangolás tekintetében a legmagasabb prioritású erőforrás számítási feladatokhoz.

Az automatikus finomhangolási mechanizmusok érett, és rendelkezik az Azure-on futó több millió adatbázison lett kedve szerint. Automatikus hangolási műveletek alkalmazása automatikusan ellenőrzése annak érdekében, hogy van egy pozitív fokozása a számítási feladatok teljesítményére. Romlott teljesítményű javaslatok dinamikusan észlelt, és azonnal vissza. A rögzített hangolási előzmények segítségével létezik egyértelmű nyomkövetési finomhangolási fejlesztések végrehajtott minden egyes Azure SQL Database-adatbázishoz. 

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

| Automatikus hangolási beállítás | Önálló adatbázis és a készletezett adatbázisok támogatása | Példány adatbázisok támogatása |
| :----------------------------- | ----- | ----- |
| **A CREATE INDEX** -indexek, amely növelheti a számítási feladatok teljesítményére, indexeli, és automatikusan ellenőrzi, hogy a lekérdezések teljesítménye javult azonosítja. | Igen | Nem | 
| **DROP INDEX** -redundáns és ismétlődő indexeket naponta, azonosítja egyedi indexek és az indexek, amelyek hosszú ideje nem használtak (> 90 nap). Vegye figyelembe, hogy jelenleg a beállítást, nem kompatibilis alkalmazások használatával a partíció közötti váltás és index mutatókat. | Igen | Nem |
| **FORCE LAST GOOD Plan csomag** – azonosítja az SQL-lekérdezések végrehajtási terv, amely alacsonyabb, mint az előző jó terv, és lekérdezi az utolsó ismert jó terv helyett a romlott csomag használatával. | Igen | Igen |

Az automatikus hangolás azonosítja **a CREATE INDEX**, **DROP INDEX**, és **KÉNYSZERÍTETT utolsó JÓ terv** optimalizálhatja az adatbázis teljesítményét, és megjeleníti őket a javaslatok [Az azure portal](sql-database-advisor-portal.md), és elérhetővé teszi azokat keresztül [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) és [REST API-val](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). 

Manuálisan alkalmazhatja a portál használatával finomhangolási ajánlásait, vagy hagyhatja, hogy az automatikus hangolás autonóm módon alkalmazhatja a hangolási javaslatokat az Ön számára. A finomhangolási ajánlásait, önállóan alkalmazza a rendszer így előnyeit van, hogy automatikusan ellenőrzi a hiba a számítási feladatok teljesítményére pozitív nyereség létezik, és ha nem észlelhető jelentős teljesítménybeli javulást, akkor automatikusan visszaállítja a hangolási javaslat. Vegye figyelembe, hogy érinti a hangolási javaslatokat, nem gyakran végrehajtott lekérdezések esetén az érvényesítési fázist eltarthat akár 72 óra elvárt. Abban az esetben, ha meg vannak manuálisan alkalmaz hangolási javaslatokat, a teljesítmény automatikus ellenőrzés és megfordítása mechanizmusok nem állnak rendelkezésre.

Az automatikus finomhangolási beállításokat is egymástól függetlenül engedélyezhető vagy letiltható adatbázisonként, vagy az SQL Database-kiszolgálók konfigurálva és minden adatbázis, amely örökli a beállításokat a kiszolgálóról a alkalmazni. SQL Database-kiszolgálók automatikus hangolási beállításai az Azure alapértelmezett is öröklik. Jelenleg az Azure alapértelmezés szerint engedélyezve van a FORCE_LAST_GOOD_PLAN, CREATE_INDEX engedélyezve van, és DROP_INDEX le van tiltva.

Automatikus finomhangolási beállításokat a kiszolgálón, és örökli a beállításokat a fölérendelt kiszolgálóhoz tartozó adatbázisok konfigurálása a konfigurálásához az automatikus hangolás, mert egyszerűbbé teszi az automatikus finomhangolási beállítások nagy számú adatbázishoz felügyeleti javasolt módszer.

## <a name="next-steps"></a>További lépések

- Engedélyezheti a a számítási feladatok kezelése Azure SQL Database automatikus finomhangolása [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md).
- Manuálisan ellenőrizze és az automatikus finomhangolási javaslatai vonatkoznak [keresse meg és teljesítménnyel kapcsolatos javaslatok alkalmazása](sql-database-advisor-portal.md).
- Automatikus finomhangolási javaslatai megtekintheti és felhasználhatja a T-SQL használatával kapcsolatban lásd: [kezelése a T-SQL-n keresztül az automatikus hangolás](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- E-mail értesítések automatikus finomhangolási ajánlásait létrehozásával kapcsolatos tudnivalókért lásd: [E-mail-értesítések automatikus finomhangolási](sql-database-automatic-tuning-email-notifications.md).
- Az automatikus hangolás használt beépített intelligenciával kapcsolatos további információkért lásd: [mesterséges intelligencia hangolja az Azure SQL Database-adatbázisok](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Az Azure SQL Database és az SQL server 2017 automatikus finomhangolási működésével kapcsolatos további információkért lásd: [SQL Server automatikus finomhangolási](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
