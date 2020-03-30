---
title: Az automatikus finomhangolás áttekintése
description: Az Azure SQL Database elemzi az SQL-lekérdezést, és automatikusan alkalmazkodik a felhasználói munkaterheléshez.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: d4762c86268353ff0464ff3919250cd86f0038d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214104"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatikus hangolás az Azure SQL Database-ben

Az Azure SQL Database automatikus hangolása csúcsteljesítményt és stabil számítási feladatokat biztosít a a mi és a gépi tanulás on alapuló folyamatos teljesítményhangolással.

Az automatikus hangolás egy teljes körűen felügyelt intelligens teljesítményszolgáltatás, amely beépített intelligenciával folyamatosan figyeli az adatbázison végrehajtott lekérdezéseket, és automatikusan javítja azok teljesítményét. Ez úgy érhető el, hogy dinamikusan alkalmazkodik az adatbázishoz a változó számítási feladatokhoz, és hangolási javaslatokat alkalmaz. Az automatikus hangolás vízszintesen tanul az Azure-on keresztül a mi-n keresztül, és dinamikusan javítja a hangolási műveleteket. Minél hosszabb ideig fut egy adatbázis automatikus hangolással, annál jobban teljesít.

Az Azure SQL Database automatikus hangolása lehet az egyik legfontosabb funkciók, amelyek lehetővé teszik, hogy stabil és csúcsteljesítményt nyújtó adatbázis-számítási feladatok.

## <a name="what-can-automatic-tuning-do-for-you"></a>Mit tehet önért az automatikus hangolás?

- Az Azure SQL-adatbázisok automatikus teljesítményhangolása
- A teljesítménynövekedés automatikus ellenőrzése
- Automatikus visszaállítás és önjavítás
- Hangolási előzmények
- T-SQL-parancsfájlok finom telepítésekhez történő finomhangolása
- Proaktív számítási feladatok teljesítményének figyelése
- Több százezer adatbázis kiskálázási képessége
- Pozitív hatás a DevOps-erőforrásokra és a teljes tulajdonlási költségre

## <a name="safe-reliable-and-proven"></a>Biztonságos, megbízható és bizonyított

Az Azure SQL Database-ben az adatbázisokra alkalmazott hangolási műveletek teljes mértékben biztonságosak a legintenzívebb számítási feladatok teljesítménye érdekében. A rendszert úgy tervezték, hogy ne zavarja a felhasználói munkaterhelést. Az automatikus hangolási javaslatok csak az alacsony kihasználtság idején kerülnek alkalmazásra. A rendszer ideiglenesen letilthatja az automatikus hangolási műveleteket is a számítási feladatok teljesítményének védelme érdekében. Ebben az esetben a "Rendszer letiltva" üzenet jelenik meg az Azure Portalon. Az automatikus hangolás a legmagasabb erőforrás-prioritású számítási feladatokat illeti meg.

Az automatikus hangolási mechanizmusok kiforrottak, és több millió Azure-on futó adatbázison lettek tökéletesítve. Az alkalmazott automatikus hangolási műveletek automatikusan ellenőrzik, hogy a számítási feladatok teljesítménye pozitív javulást eredményezett-e. A regressziós teljesítményjavaslatok dinamikusan észlelhetők, és azonnal visszaállnak. A hangolási előzmények rögzített, létezik egy egyértelmű nyoma az egyes Azure SQL-adatbázis hangolási fejlesztések.

![Hogyan működik az automatikus hangolás?](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Az Azure SQL Database automatikus hangolása megosztja az alapvető logikát az SQL Server automatikus hangolási motorjával. A beépített intelligenciamechanizmusról további technikai információkat az [SQL Server automatikus hangolása](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)című témakörben talál.

Az automatikus hangolás működésének áttekintését és a tipikus használati forgatókönyveket a beágyazott videóban találja:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Automatikus hangolás engedélyezése

Engedélyezheti [az automatikus hangolás egy- és készletezésű adatbázisok az Azure Portalon,](sql-database-automatic-tuning-enable.md) vagy az [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL utasítás használatával. Az [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL utasítás használatával engedélyezheti a felügyelt példányok központi telepítésében lévő példányadatbázisok automatikus hangolását.

## <a name="automatic-tuning-options"></a>Automatikus hangolási beállítások

Az Azure SQL Database automatikus hangolási lehetőségei a következők:

| Automatikus hangolási lehetőség | Egyadatbázis- és készletezésű adatbázis-támogatás | Példányadatbázis-támogatás |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** – Azonosítja azokat az indexeket, amelyek javíthatják a számítási feladatok teljesítményét, indexeket hozhatnak létre, és automatikusan ellenőrzik, hogy javult-e a lekérdezések teljesítménye. | Igen | Nem |
| **DROP INDEX** - Azonosítja a redundáns és ismétlődő indexek naponta, kivéve az egyedi indexek, és az indexek, amelyek nem használták hosszú ideig (>90 nap). Kérjük, vegye figyelembe, hogy ez a beállítás nem kompatibilis a partíciókapcsolást és indextippeket használó alkalmazásokkal. A nem használt indexek eldobása nem támogatott a prémium és az üzleti legkritikusabb szolgáltatási szinteken. | Igen | Nem |
| **FORCE LAST GOOD PLAN** (automatikus tervjavítás) – Azonosítja az SQL-lekérdezéseket az előző jó tervnél lassabb végrehajtási terv vel, és a regressziós terv helyett az utolsó ismert jó tervet használó lekérdezéseket. | Igen | Igen |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Automatikus hangolás egy- és összevont adatbázisokhoz

Az egy- és készletezésű adatbázisok automatikus hangolása a **CREATE INDEX**, **DROP INDEX**és A FORCE LAST **GOOD PLAN** adatbázis-tanácsadói javaslatokat használja az adatbázis teljesítményének optimalizálásához. További információ: [Database advisor recommendations in the Azure portal](sql-database-advisor-portal.md), in [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction), and in the [REST API.](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

Manuálisan is alkalmazhat hangolási javaslatokat az Azure Portalon, vagy lehetővé teheti az automatikus hangolási autonóm hangolási javaslatok at. A rendszer által önállóan alkalmazott hangolási javaslatok lehetővé teszik, hogy automatikusan ellenőrizze, hogy a számítási feladatok teljesítménye pozitív nyereségre tesz szert, és ha nem észlel jelentős teljesítményjavulást, akkor automatikusan visszaállítja a hangolási javaslatot. Kérjük, vegye figyelembe, hogy a gyakran nem végrehajtott finomhangolási javaslatok által érintett lekérdezések esetén az érvényesítési fázis akár 72 órás tervezési szakaszt is igénybe vehet.

Abban az esetben, ha a T-SQL-en keresztül alkalmazza a hangolási javaslatokat, az automatikus teljesítmény-érvényesítés és a sztornírozási mechanizmusok nem érhetők el. Az ilyen módon alkalmazott ajánlások aktívak maradnak, és a 24-48 órás hangolási ajánlások listájában jelennek meg. mielőtt a rendszer automatikusan visszavonja azokat. Ha hamarabb szeretne eltávolítani egy javaslatot, elvetheti azt az Azure Portalról.

Az automatikus hangolási beállítások adatbázisonként egymástól függetlenül engedélyezhetők vagy letilthatók, vagy konfigurálhatók az SQL Database kiszolgálóin, és alkalmazhatók minden olyan adatbázison, amely a beállításokat a kiszolgálótól örökli. Az SQL Database-kiszolgálók örökölhetik az Azure alapértelmezett beállításait az automatikus hangolási beállításokhoz. Az Azure alapértelmezései ebben az időben vannak beállítva, FORCE_LAST_GOOD_PLAN engedélyezve van, CREATE_INDEX engedélyezve van, és DROP_INDEX le van tiltva.

> [!IMPORTANT]
> Márciustól az automatikus hangoláshoz 2020-ig az Azure alapértelmezett módosításai a következőképpen lépnek érvénybe:
>
> - Az új Azure-beállítások FORCE_LAST_GOOD_PLAN = engedélyezve lesznek, CREATE_INDEX = letiltva és DROP_INDEX = letiltva.
> - Az automatikus hangolási beállításokkal nem rendelkező meglévő kiszolgálók automatikusan konfigurálva lesznek az új Azure-alapértelmezett értékek örökléséhez. Ez minden olyan ügyfélre vonatkozik, akik nek jelenleg nincs kiszolgálóbeállítása az automatikus hangoláshoz, meghatározatlan állapotban.
> - Az új onnan létrehozott új kiszolgálók automatikusan úgy lesznek konfigurálva, hogy örököljék az új Azure-alapértelmezett értékeket (ellentétben a korábbiakkal, amikor az automatikus hangolási konfiguráció nem definiált állapotban volt az új kiszolgáló létrehozásakor).

A kiszolgálón az automatikus hangolási beállítások konfigurálása és a szülőkiszolgálóhoz tartozó adatbázisok beállításainak öröklése ajánlott módszer az automatikus hangolás konfigurálására, mivel leegyszerűsíti az automatikus hangolási beállítások kezelését számos adatbázis esetében.

Ha többet szeretne tudni az automatikus hangolási javaslatokhoz való e-mail értesítések létrehozásáról, olvassa el [az Automatikus hangolásról szóló e-mail értesítések című témakört.](sql-database-automatic-tuning-email-notifications.md)

### <a name="automatic-tuning-for-instance-databases"></a>Például adatbázisok automatikus hangolása

Automatikus hangolás például adatbázisok egy felügyelt példány telepítése csak támogatja **a FORCE LAST GOOD PLAN**. Az automatikus hangolási beállítások T-SQL-en keresztüli konfigurálásáról az [Automatikus hangolás bevezeti az automatikus tervkorrekciót](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) és [az Automatikus tervjavítás című](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)témakört tartalmazza.

## <a name="next-steps"></a>További lépések

- Az automatikus hangolásban használt beépített intelligenciáról a [Mesterséges intelligencia az Azure SQL-adatbázisainak finomhangolása című témakörben olvashat.](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)
