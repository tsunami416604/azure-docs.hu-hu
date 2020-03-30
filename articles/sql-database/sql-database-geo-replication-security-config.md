---
title: A vészhelyreállítás biztonságának konfigurálása
description: Ismerje meg a biztonsági szempontokat az adatbázis-visszaállítás vagy egy másodlagos kiszolgálóra történő feladatátvétel után a biztonság konfigurálásához és kezeléséhez.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 9d628583168883276e67d9e2f2fcafdce292769e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73807501"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Az Azure SQL Database biztonságának konfigurálása és kezelése geo-visszaállításhoz vagy feladatátvételhez

Ez a cikk az [aktív georeplikációs](sql-database-active-geo-replication.md) és [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)konfigurálásához és vezérléséhez szükséges hitelesítési követelményeket ismerteti. A másodlagos adatbázis felhasználói hozzáférésének beállításához szükséges lépéseket is tartalmazza. Végül azt is leírja, hogyan engedélyezheti a hozzáférést a helyreállított adatbázishoz a [geo-visszaállítás](sql-database-recovery-using-backups.md#geo-restore)használata után. A helyreállítási beállításokkal kapcsolatos további információkért lásd: [Üzletmenet-folytonosság – áttekintés.](sql-database-business-continuity.md)

## <a name="disaster-recovery-with-contained-users"></a>Katasztrófa utáni helyreállítás a tartalmazott felhasználókkal

A hagyományos felhasználókkal ellentétben, amelyeket le kell képezni a fő adatbázisba való bejelentkezéshez, a tárolt felhasználókat teljes mértékben maga az adatbázis kezeli. Ennek két előnye van. A vész-helyreállítási forgatókönyvben a felhasználók továbbra is csatlakozhatnak az új elsődleges adatbázishoz vagy a geo-visszaállítás használatával helyreállított adatbázishoz további konfiguráció nélkül, mivel az adatbázis kezeli a felhasználókat. Vannak is lehetséges méretezhetőség és a teljesítmény előnyeit ez a konfiguráció egy bejelentkezési szempontból. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://msdn.microsoft.com/library/ff929188.aspx) cikkben talál.

A fő kompromisszum az, hogy a vész-helyreállítási folyamat nagy mértékű kezelése nagyobb kihívást jelent. Ha több adatbázis, amely ugyanazt a bejelentkezést használja, a hitelesítő adatok fenntartása a több adatbázisban lévő felhasználók használatával megakadályozhatja a tartalmazott felhasználók előnyeit. A jelszóelforgatási házirend például megköveteli, hogy a módosításokat több adatbázisban következetesen hajtsák végre, ahelyett, hogy a fő adatbázisban egyszer módosítanák a bejelentkezés jelszavát. Ezért ha több olyan adatbázisa van, amely ugyanazt a felhasználónevet és jelszót használja, a tartalmazott felhasználók használata nem ajánlott.

## <a name="how-to-configure-logins-and-users"></a>A bejelentkezések és a felhasználók konfigurálása

Ha bejelentkezést és felhasználót használ (nem pedig a tartalmazott felhasználókat), további lépéseket kell tennie annak biztosítására, hogy ugyanazok a bejelentkezések létezzenek a fő adatbázisban. A következő szakaszok ismertetik a szükséges lépéseket és a további szempontokat.

  >[!NOTE]
  > Az Azure Active Directory (AAD) bejelentkezések használatával is kezelheti az adatbázisokat. További információ: [Azure SQL loginek and users.](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Felhasználói hozzáférés beállítása másodlagos vagy helyreállított adatbázishoz

Annak érdekében, hogy a másodlagos adatbázis csak olvasható másodlagos adatbázisként használható legyen, és hogy megfelelő hozzáférést biztosítson az új elsődleges adatbázishoz vagy a geo-visszaállítás sal helyreállított adatbázishoz, a célkiszolgáló fő adatbázisának megfelelő biztonsággal kell rendelkeznie. a helyreállítás előtt.

Az egyes lépcsők höz tartozó engedélyeket a témakör későbbi részében ismertetjük.

A georeplikációmásodlagos georeplikációs másodlagos felhasználói hozzáférésének előkészítése a georeplikáció részeként történik. A felhasználó hozzáférésének előkészítése a geo-visszaállított adatbázisokhoz bármikor elvégezhető, amikor az eredeti kiszolgáló online állapotban van (pl. a VÉSZ-fúró részeként).

> [!NOTE]
> Ha feladatátvételt vagy geo-visszaállítást végez egy olyan kiszolgálóra, amely nem rendelkezik megfelelően konfigurált bejelentkezésekkel, a hozzáférés a kiszolgáló felügyeleti fiókjára korlátozódik.

A bejelentkezések beállítása a célkiszolgálón az alábbiakban ismertetett három lépésből áll:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Az elsődleges adatbázishoz való hozzáféréssel rendelkező bejelentkezések meghatározása

A folyamat első lépése annak meghatározása, hogy mely bejelentkezéseket kell duplikálni a célkiszolgálón. Ez egy pár SELECT utasítással történik, egyet a forráskiszolgáló logikai főadatbázisában, egyet pedig magában az elsődleges adatbázisban.

A következő SELECT utasítással csak a kiszolgáló rendszergazdája vagy a **LoginManager** kiszolgálói szerepkör egyik tagja határozhatja meg a forráskiszolgálón lévő bejelentkezéseket.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Csak a db_owner adatbázis-szerepkör tagja, a dbo-felhasználó vagy a kiszolgáló rendszergazdája határozhatja meg az elsődleges adatbázis összes felhasználói felhasználójának egyszerű tagját.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Az 1.

Az előző szakaszlekérdezéseinek kimenetének összehasonlításával és a biztonsági azonosítók egyeztetésével leképezheti a kiszolgálóbejelentkezést az adatbázis-felhasználóhoz. Azok a bejelentkezések, amelyek egy ezres biztonsági azonosítóval rendelkező adatbázis-felhasználóval rendelkeznek, az adatbázis hoz való felhasználói hozzáféréssel rendelkeznek az adatbázis egyszerű felhasználójaként.

A következő lekérdezés segítségével megtekintheti az összes felhasználó egyszerű és a biztonsági azonosítók egy adatbázisban. Ezt a lekérdezést csak a db_owner adatbázis-szerepkör vagy a kiszolgáló rendszergazdájának egy tagja futtathatja.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> A **INFORMATION_SCHEMA** és **sys** felhasználók *NULL* SID azonosítókkal rendelkeznek, a **vendég** biztonsági azonosítója **pedig 0x00**. A **dbo** SID a *0x01060000000000000000000000000000000000000000000048454*típusú kiszolgálóval kezdheti , ha az adatbázis-létrehozó a **DbManager**helyett a kiszolgáló adminisztrátora volt.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Hozza létre a bejelentkezéseket a célkiszolgálón

Az utolsó lépés az, hogy menjen a célszerver, vagy a szerverek, és létrehozza a bejelentkezések a megfelelő azonosítók. Az alapszintaxis a következő.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Ha a másodlagos, de az elsődleges hez nem hozzáférést szeretne adni a felhasználónak, ezt megteheti az elsődleges kiszolgálón a felhasználói bejelentkezés módosításával az alábbi szintaxis használatával.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> A DISABLE nem módosítja a jelszót, így szükség esetén bármikor engedélyezheti.

## <a name="next-steps"></a>További lépések

* Az adatbázis-hozzáférés és -bejelentkezések kezeléséről az [SQL Database biztonsága: Adatbázis-hozzáférés és bejelentkezésbiztonság kezelése](sql-database-manage-logins.md)című témakörben talál további információt.
* A tartalmazott adatbázis-felhasználókról további információt a [Tartalmazott adatbázis-felhasználók – Az adatbázis hordozhatóvá tétele](https://msdn.microsoft.com/library/ff929188.aspx)című témakörben talál.
* Az aktív georeplikációról az [Aktív georeplikáció](sql-database-active-geo-replication.md)című témakörben olvashat.
* Az automatikus feladatátvételi csoportokról az [Automatikus feladatátvételi csoportok (Auto-feladatátvételi csoportok)](sql-database-auto-failover-group.md)témakörben olvashat.
* A geo-visszaállítás használatáról a [geo-visszaállítás című](sql-database-recovery-using-backups.md#geo-restore) témakörben talál
