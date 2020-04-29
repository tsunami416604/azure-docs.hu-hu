---
title: Az automatikus finomhangolás áttekintése
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
ms.date: 03/30/2020
ms.openlocfilehash: 7488fd5f8a572788933856f03bb0ad4351885704
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80518226"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatikus hangolás a Azure SQL Databaseban

Az automatikus hangolás Azure SQL Database a teljesítmény és a stabil számítási feladatok folyamatos teljesítmény-hangolással, az AI és a gépi tanulás alapján.

Az automatikus hangolás egy teljes körűen felügyelt intelligens teljesítményű szolgáltatás, amely beépített intelligenciát használ az adatbázison végrehajtott lekérdezések folyamatos figyeléséhez, és automatikusan javítja a teljesítményt. Ez az adatbázis dinamikusan alkalmazkodik a változó munkaterhelésekhez és a hangolási javaslatok alkalmazásához. Az automatikus hangolás az Azure-on keresztül az összes Azure-beli adatbázisból horizontálisan tanul, és dinamikusan javítja a hangolási műveleteit. Minél tovább fut az adatbázis automatikus finomhangolása, annál jobb.

Az automatikus hangolás Azure SQL Database az egyik legfontosabb funkciója lehet, amellyel stabil és maximális teljesítményű adatbázis-számítási feladatokat biztosíthat.

## <a name="what-can-automatic-tuning-do-for-you"></a>Mire használható az automatikus Finomhangolás

- Az Azure SQL Database-adatbázisok automatikus teljesítményének finomhangolása
- A teljesítmény-nyereség automatikus ellenőrzése
- Automatikus visszaállítás és saját javítás
- Hangolási előzmények
- A T-SQL-parancsfájlok kézi üzembe helyezéshez való finomhangolása
- Proaktív számítási feladatok teljesítményének figyelése
- Több százezer adatbázisra kibővíthető képesség
- Pozitív hatással van a DevOps-erőforrásokra és a teljes tulajdonlási díjakra

## <a name="safe-reliable-and-proven"></a>Biztonságos, megbízható és bevált

A Azure SQL Database adatbázisain alkalmazott hangolási műveletek teljes mértékben biztonságosak a legintenzívebb számítási feladatok teljesítményében. A rendszer olyan gondossággal lett kialakítva, hogy ne zavarja a felhasználói munkaterheléseket. Az Automatikus hangolási javaslatok csak alacsony kihasználtságú esetekben alkalmazhatók. A szolgáltatás átmenetileg letilthatja az Automatikus hangolási műveleteket a munkaterhelés teljesítményének biztosítása érdekében. Ilyen esetben a "rendszer által letiltott" üzenet jelenik meg Azure Portalban. Az automatikus hangolás a legmagasabb erőforrás-prioritással rendelkező számítási feladatokat tartja.

Az Automatikus hangolási mechanizmusok kiforrtak, és az Azure-on futó több millió adatbázison is tökéletesítve lettek. Az alkalmazott Automatikus hangolási műveletek ellenőrzése automatikusan megtörténik, így biztosítva, hogy a számítási feladatok teljesítménye pozitívan javuljon. A romlott teljesítményével kapcsolatos javaslatok dinamikusan észlelhetők és azonnal visszaállíthatók. A hangolási előzményeken keresztül az egyes Azure SQL Databaseek hangolási funkcióinak egyértelmű nyomon követése áll fenn.

![Hogyan működik az automatikus hangolás?](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database automatikus hangolás a SQL Server Automatikus hangolási motorral osztja meg az alapvető logikát. A beépített intelligencia mechanizmussal kapcsolatos további technikai információkért lásd: [SQL Server automatikus hangolás](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

Az automatikus hangolás működésének és a tipikus használati forgatókönyvek áttekintését lásd a beágyazott videóban:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Automatikus hangolás engedélyezése

Az [automatikus hangolást engedélyezheti a Azure Portalban található önálló és készletezett adatbázisokhoz](sql-database-automatic-tuning-enable.md) vagy az [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL-utasítás használatával. Az [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL-utasítás használatával engedélyezheti az automatikus finomhangolást a felügyelt példányok központi telepítésében.

## <a name="automatic-tuning-options"></a>Automatikus hangolási beállítások

A Azure SQL Databaseban elérhető Automatikus hangolási lehetőségek a következők:

| Automatikus hangolási beállítás | Önálló adatbázis és készletezett adatbázis-támogatás | Példány-adatbázis támogatása |
| :----------------------------- | ----- | ----- |
| **Index létrehozása** – azonosítja azokat az indexeket, amelyek javíthatják a munkaterhelés teljesítményét, indexeket hoz létre, és automatikusan ellenőrzi, hogy a lekérdezések teljesítménye javult-e. | Igen | Nem |
| **Drop index** – naponta azonosítja a redundáns és ismétlődő indexeket, kivéve azokat az egyedi indexeket és indexeket, amelyeket hosszú ideje nem használtak (>90 nap). Vegye figyelembe, hogy ez a beállítás nem kompatibilis a partíciós váltást és az indexelési tippeket használó alkalmazásokkal. A nem használt indexek nem támogatottak a prémium és üzletileg kritikus szolgáltatási szinteken. | Igen | Nem |
| **Utolsó jó csomag kényszerítése** (automatikus terv helyesbítése) – az SQL-lekérdezéseket az előző helyes csomagnál lassabb végrehajtási terv alapján azonosítja, és a romlott terv helyett az utolsó ismert helyes terv használatával kérdezi le a lekérdezéseket. | Igen | Igen |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Önálló és készletezett adatbázisok automatikus finomhangolása

Az önálló és a készletezett adatbázisok automatikus finomhangolása a **create index**, a **drop index**és a **Last Good Plan** Database Advisor javaslatait használja az adatbázis teljesítményének optimalizálása érdekében. További információ: az [adatbázis-tanácsadói javaslatok a Azure Portal](sql-database-advisor-portal.md), a [PowerShellben](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)és a [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Manuálisan alkalmazhatja a hangolási javaslatokat a Azure Portal használatával, vagy engedélyezheti az automatikus finomhangolást a hangolási javaslatok önálló alkalmazásával. A rendszer önálló finomhangolási javaslatok használatának előnye, hogy automatikusan ellenőrzi, hogy van-e pozitív nyereség a munkaterhelés teljesítményében, és ha nem észlelhető jelentős teljesítmény-javulás, automatikusan visszaállíthatja a hangolási javaslatot. Vegye figyelembe, hogy a gyakran nem végrehajtott hangolási javaslatok által érintett lekérdezések esetében az ellenőrzési fázis akár 72 órával is eltarthat.

Ha T-SQL-n keresztül alkalmazza a hangolási javaslatokat, az automatikus teljesítmény-ellenőrzés és a megfordítási mechanizmusok nem érhetők el. Az ilyen módon alkalmazott javaslatok továbbra is aktívak maradnak, és megjelennek a 24-48 óra hangolási javaslatainak listájában. mielőtt a rendszer automatikusan visszavonja őket. Ha előbb el szeretné távolítani a javaslatot, elvetheti Azure Portalból.

Az Automatikus hangolási beállítások egymástól függetlenül engedélyezhetők vagy letilthatók az adatbázison, vagy konfigurálható SQL Database-kiszolgálókon, és minden olyan adatbázison alkalmazható, amely a kiszolgáló beállításait örökli. SQL Database-kiszolgálók öröklik az Azure alapértelmezett beállításait az Automatikus hangolási beállításokhoz. Az Azure alapértelmezett beállításai jelenleg FORCE_LAST_GOOD_PLAN engedélyezve vannak, CREATE_INDEX engedélyezve van, és a DROP_INDEX le van tiltva.

> [!IMPORTANT]
> Március 2020 az automatikus hangoláshoz tartozó Azure-beli alapértelmezett beállítások változásai az alábbiak szerint lépnek életbe:
>
> - Az új Azure alapértékek FORCE_LAST_GOOD_PLAN = engedélyezve, CREATE_INDEX = letiltva, és DROP_INDEX = letiltva lesznek.
> - Az Automatikus hangolási beállítások konfigurálását nem tartalmazó meglévő kiszolgálók automatikusan konfigurálva lesznek az új Azure-alapértékek ÖRÖKLÉSére. Ez minden olyan ügyfélre vonatkozik, amely jelenleg nem definiált állapotban van az Automatikus hangolási kiszolgáló beállításaival.
> - A létrehozott új kiszolgálók automatikusan úgy lesznek konfigurálva, hogy ÖRÖKÖLje az új Azure-alapértékeket (a korábbitól eltérően, ha az Automatikus hangolási konfiguráció nem definiált állapotú az új kiszolgáló létrehozása után).

Ha az Automatikus hangolási beállításokat egy kiszolgálón konfigurálja, és a fölérendelt kiszolgálóhoz tartozó adatbázisok beállításait örökli, ajánlott módszer az automatikus Finomhangolás konfigurálására, mivel az leegyszerűsíti a nagy számú adatbázis Automatikus hangolási beállításainak kezelését.

Az Automatikus hangolási javaslatokra vonatkozó e-mail-értesítések létrehozásával kapcsolatos további információkért lásd: [e-mail-értesítések az automatikus hangoláshoz](sql-database-automatic-tuning-email-notifications.md).

### <a name="automatic-tuning-for-instance-databases"></a>Példány-adatbázisok automatikus finomhangolása

A felügyelt példányok központi telepítésének példány-adatbázisainak automatikus finomhangolása csak a **kényszerített utolsó helyes tervet**támogatja. Az Automatikus hangolási beállítások T-SQL-en keresztüli konfigurálásával kapcsolatos további információkért lásd: az automatikus [hangolás bevezeti az automatikus terv javítását](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) és az [automatikus terv javítását](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>További lépések

- Az automatikus hangolásban használt beépített intelligenciával kapcsolatos információkért lásd: [mesterséges intelligencia az Azure SQL Database-adatbázisok](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Annak megismeréséhez, hogy az automatikus hangolás hogyan működik a motorháztető alatt, tekintse meg a [több millió adatbázis automatikus indexelése Microsoft Azure SQL Databaseban](https://www.microsoft.com/en-us/research/uploads/prod/2019/02/autoindexing_azuredb.pdf)című témakört.
