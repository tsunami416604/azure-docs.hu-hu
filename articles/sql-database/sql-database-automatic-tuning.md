---
title: Azure SQL Database – automatikus hangolás
description: Azure SQL Database elemzi az SQL-lekérdezést, és automatikusan alkalmazkodik a felhasználói munkaterheléshez.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/06/2019
ms.openlocfilehash: bfac5a0eba68469d912efd02699624e1335e40e5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691108"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatikus hangolás a Azure SQL Databaseban

Az automatikus hangolás Azure SQL Database a teljesítmény és a stabil számítási feladatok folyamatos teljesítmény-hangolással, az AI és a gépi tanulás alapján.

Az automatikus hangolás egy teljes körűen felügyelt intelligens teljesítményű szolgáltatás, amely beépített intelligenciát használ az adatbázison végrehajtott lekérdezések folyamatos figyeléséhez, és automatikusan javítja a teljesítményt. Ez az adatbázis dinamikusan alkalmazkodik a változó munkaterhelésekhez és a hangolási javaslatok alkalmazásához. Az automatikus hangolás az Azure-on keresztül az összes Azure-beli adatbázisból horizontálisan tanul, és dinamikusan javítja a hangolási műveleteit. Minél több Azure SQL Database fut automatikus hangolással, annál hatékonyabban végezhető el.

Az automatikus hangolás Azure SQL Database az egyik legfontosabb funkciója lehet, amellyel stabil és maximális teljesítményű adatbázis-számítási feladatokat biztosíthat.

## <a name="what-can-automatic-tuning-do-for-you"></a>Mire használható az automatikus Finomhangolás?

- Az Azure SQL Database-adatbázisok automatikus teljesítményének finomhangolása
- A teljesítmény-nyereség automatikus ellenőrzése
- Automatikus visszaállítás és saját javítás
- Hangolási előzmények
- A T-SQL-parancsfájlok kézi üzembe helyezéshez való finomhangolása
- Proaktív számítási feladatok teljesítményének figyelése
- Több százezer adatbázisra kibővíthető képesség
- Pozitív hatással van a DevOps-erőforrásokra és a teljes tulajdonlási díjakra

## <a name="safe-reliable-and-proven"></a>Biztonságos, megbízható és bevált

Az Azure SQL Database-adatbázisokra alkalmazott hangolási műveletek teljes mértékben biztonságosak a legintenzívebb számítási feladatok teljesítményében. A rendszer olyan gondossággal lett kialakítva, hogy ne zavarja a felhasználói munkaterheléseket. Az Automatikus hangolási javaslatok csak alacsony kihasználtságú esetekben alkalmazhatók. A szolgáltatás átmenetileg letilthatja az Automatikus hangolási műveleteket a munkaterhelés teljesítményének biztosítása érdekében. Ilyen esetben a "rendszer által letiltott" üzenet jelenik meg Azure Portalban. Az automatikus hangolás a legmagasabb erőforrás-prioritással rendelkező számítási feladatokat tartja.

Az Automatikus hangolási mechanizmusok kiforrtak, és az Azure-on futó több millió adatbázison is tökéletesítve lettek. Az alkalmazott Automatikus hangolási műveletek ellenőrzése automatikusan megtörténik, így biztosítva, hogy a számítási feladatok teljesítménye pozitívan javuljon. A romlott teljesítményével kapcsolatos javaslatok dinamikusan észlelhetők és azonnal visszaállíthatók. A hangolási előzményeken keresztül az egyes Azure SQL Databaseek hangolási funkcióinak egyértelmű nyomon követése áll fenn. 

![Hogyan működik az automatikus hangolás?](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database automatikus hangolás a SQL Server Automatikus hangolási motorral osztja meg az alapvető logikát. A beépített intelligencia mechanizmussal kapcsolatos további technikai információkért lásd: [SQL Server automatikus hangolás](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Automatikus hangolás használata

Az automatikus hangolást engedélyezni kell az előfizetésben. Az automatikus hangolás Azure Portal használatával történő engedélyezéséről az [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md)című témakörben olvashat.

Az automatikus hangolás önállóan is működhet a hangolási javaslatok automatikus alkalmazása révén, beleértve a teljesítménybeli nyereségek automatizált ellenőrzését is. 

További szabályozás érdekében a hangolási javaslatok automatikus alkalmazása kikapcsolható, és a javaslatok manuális alkalmazása Azure Portal használatával végezhető el. Lehetőség van arra is, hogy a megoldással csak az Automatikus hangolási javaslatokat tekintse meg, és manuálisan alkalmazza azokat parancsfájlok és eszközök használatával. 

Az automatikus hangolás működésének és a tipikus használati forgatókönyvek áttekintését lásd a beágyazott videóban:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Automatikus hangolási beállítások

A Azure SQL Databaseban elérhető Automatikus hangolási lehetőségek a következők:

| Automatikus hangolási beállítás | Önálló adatbázis és készletezett adatbázis-támogatás | Példány-adatbázis támogatása |
| :----------------------------- | ----- | ----- |
| **Index létrehozása** – azonosítja azokat az indexeket, amelyek javíthatják a munkaterhelés teljesítményét, indexeket hoz létre, és automatikusan ellenőrzi, hogy a lekérdezések teljesítménye javult-e. | Igen | Nem | 
| **Drop index** – naponta azonosítja a redundáns és ismétlődő indexeket, kivéve azokat az egyedi indexeket és indexeket, amelyeket nem használtak hosszú ideig (> 90 nap). Vegye figyelembe, hogy ez a beállítás nem kompatibilis a partíciós váltást és az indexelési tippeket használó alkalmazásokkal. A nem használt indexek nem támogatottak a prémium és üzletileg kritikus szolgáltatási szinteken. | Igen | Nem |
| **Utolsó jó csomag kényszerítése** (automatikus terv helyesbítése) – az SQL-lekérdezéseket az előző helyes csomagnál lassabb végrehajtási terv alapján azonosítja, és a romlott terv helyett az utolsó ismert helyes terv használatával kérdezi le a lekérdezéseket. | Igen | Igen |

Az automatikus hangolás azonosítja a **create index**, a **drop index**és az **utolsó jó csomagra** vonatkozó ajánlásokat, amelyek optimalizálják az adatbázis teljesítményét, és megjelenítik őket a [Azure PORTALban](sql-database-advisor-portal.md), és a [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) [-en keresztül is elérhetővé válnak. REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). Ha többet szeretne megtudni a KÉNYSZERÍTett utolsó jó CSOMAGról és az Automatikus hangolási lehetőségekről a T-SQL-en keresztül, tekintse meg az automatikus [hangolás](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)bevezetését.

Manuálisan is alkalmazhatja a hangolási javaslatokat a portál használatával, vagy engedélyezheti az automatikus finomhangolást a hangolási javaslatok önálló alkalmazásával. Az előnye, hogy a rendszer önállóan alkalmazza a hangolási javaslatokat, hogy automatikusan ellenőrizze, hogy van-e pozitív nyereség a munkaterhelés teljesítményében, és ha nem észlelhető jelentős teljesítmény-javulás, akkor a hangolási javaslat automatikus visszaállítása. Vegye figyelembe, hogy a gyakran nem végrehajtott hangolási javaslatok által érintett lekérdezések esetében az ellenőrzési fázis akár 72 órával is eltarthat.

Ha T-SQL-n keresztül alkalmazza a hangolási javaslatokat, az automatikus teljesítmény-ellenőrzés és a megfordítási mechanizmusok nem érhetők el. Az ilyen módon alkalmazott javaslatok továbbra is aktívak maradnak, és megjelennek a 24-48 óra hangolási javaslatainak listájában. mielőtt a rendszer automatikusan visszavonja őket. Ha előbb el szeretné távolítani a javaslatot, elvetheti Azure Portalból.

Az Automatikus hangolási beállítások egymástól függetlenül engedélyezhetők vagy letilthatók az adatbázison, vagy konfigurálható SQL Database-kiszolgálókon, és minden olyan adatbázison alkalmazható, amely a kiszolgáló beállításait örökli. SQL Database-kiszolgálók öröklik az Azure alapértelmezett beállításait az Automatikus hangolási beállításokhoz. Az Azure alapértelmezett értékei a FORCE_LAST_GOOD_PLAN beállítás engedélyezve van, a CREATE_INDEX engedélyezve van, és a DROP_INDEX le van tiltva.

Ha az Automatikus hangolási beállításokat egy kiszolgálón konfigurálja, és a fölérendelt kiszolgálóhoz tartozó adatbázisok beállításait örökli, ajánlott módszer az automatikus Finomhangolás konfigurálására, mivel az leegyszerűsíti a nagy számú adatbázis Automatikus hangolási beállításainak kezelését.

## <a name="next-steps"></a>További lépések

- A számítási feladatok kezeléséhez Azure SQL Database automatikus hangolásának engedélyezéséhez tekintse meg az [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md)című témakört.
- Az Automatikus hangolási javaslatok manuális áttekintéséhez és alkalmazásához tekintse [meg a teljesítményre vonatkozó javaslatok keresése és alkalmazása](sql-database-advisor-portal.md)című témakört.
- Ha szeretné megtudni, hogyan használhatja a T-SQL-T az Automatikus hangolási javaslatok alkalmazására és megtekintésére, tekintse meg a [t-SQL-n keresztüli automatikus hangolás kezelése](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)
- Az Automatikus hangolási javaslatokra vonatkozó e-mail-értesítések létrehozásával kapcsolatos további információkért lásd: [e-mail-értesítések az automatikus hangoláshoz](sql-database-automatic-tuning-email-notifications.md).
- Az automatikus hangolásban használt beépített intelligenciával kapcsolatos információkért lásd: [mesterséges intelligencia az Azure SQL Database-adatbázisok](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Ha szeretne többet megtudni arról, hogy az automatikus hangolás hogyan működik Azure SQL Database és az SQL Server 2017-ben, tekintse meg az [automatikus hangolás SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
