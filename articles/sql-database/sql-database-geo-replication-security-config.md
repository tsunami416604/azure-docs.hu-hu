---
title: Azure SQL Database Biztonság beállítása a vész-helyreállításhoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja és kezelheti a biztonsági szempontokat egy adatbázis-visszaállítás vagy egy másodlagos kiszolgáló feladatátvétele után.
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
ms.openlocfilehash: 4d4939b7a0179216d11f594ce12f384276d15e05
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568126"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Azure SQL Database biztonsági beállítások konfigurálása és kezelése geo-visszaállításhoz vagy feladatátvételhez

Ez a cikk az [aktív földrajzi replikálási](sql-database-active-geo-replication.md) és [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)konfigurálásának és vezérlésének hitelesítési követelményeit ismerteti. Emellett a másodlagos adatbázishoz való felhasználói hozzáférés beállításához szükséges lépéseket is megadja. Végezetül azt is leírja, hogyan engedélyezheti a helyreállított adatbázishoz való hozzáférést a [geo-visszaállítás](sql-database-recovery-using-backups.md#geo-restore)használata után. További információ a helyreállítási lehetőségekről: az [üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md).

## <a name="disaster-recovery-with-contained-users"></a>Vész-helyreállítás a befoglalt felhasználókkal

A hagyományos felhasználóktól eltérően, amelyeket a Master adatbázisban lévő bejelentkezésekhez kell leképezni, a tárolt felhasználókat az adatbázis teljes mértékben kezeli. Ez két előnnyel jár. A vész-helyreállítási forgatókönyvben a felhasználók továbbra is csatlakozhatnak az új elsődleges adatbázishoz vagy az adatbázis helyreállításához a Geo-visszaállítás használatával, további konfiguráció nélkül, mert az adatbázis kezeli a felhasználókat. A konfiguráció a bejelentkezési perspektívából is lehetséges méretezhetőségi és teljesítménybeli előnyökkel jár. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://msdn.microsoft.com/library/ff929188.aspx) cikkben talál.

A fő kompromisszum az, hogy a vész-helyreállítási folyamat nagyobb kihívást jelent. Ha több adatbázissal is rendelkezik, amelyek ugyanazt a bejelentkezést használják, a hitelesítő adatoknak a több adatbázisba foglalt felhasználókkal való megőrzése megtagadhatja a befoglalt felhasználók előnyeit. Például a jelszó-elforgatási házirend megköveteli, hogy a módosítások a főadatbázisban egyszer a bejelentkezéshez használt jelszó módosítása helyett több adatbázisban is konzisztensek legyenek. Ezért ha több olyan adatbázissal rendelkezik, amelyek ugyanazt a felhasználónevet és jelszót használják, a foglalt felhasználók használata nem ajánlott.

## <a name="how-to-configure-logins-and-users"></a>Bejelentkezések és felhasználók konfigurálása

Ha bejelentkezési adatokat és felhasználókat használ (nem a tárolt felhasználók helyett), akkor további lépéseket kell tennie annak biztosításához, hogy ugyanazokat a bejelentkezéseket a Master adatbázis tartalmazza. A következő fejezetek az érintett lépéseket és a további szempontokat ismertetik.

  >[!NOTE]
  > Az adatbázisok kezeléséhez Azure Active Directory (HRE) bejelentkezés is lehetséges. További információ: [Azure SQL-bejelentkezések és felhasználók](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Felhasználói hozzáférés beállítása másodlagos vagy helyreállított adatbázishoz

Ahhoz, hogy a másodlagos adatbázis csak olvasható másodlagos adatbázisként legyen felhasználható, és hogy megfelelő hozzáférést biztosítson az új elsődleges adatbázishoz vagy a helyreállított adatbázishoz a Geo-visszaállítás használatával, a célkiszolgáló főadatbázisának rendelkeznie kell a megfelelő biztonsággal a helyreállítás előtt a konfiguráció van érvényben.

Az egyes lépésekhez tartozó konkrét engedélyekről a témakör későbbi részében olvashat.

A Geo-replikációhoz való felhasználói hozzáférés előkészítését a Geo-replikáció konfigurálásának részeként kell végrehajtani. Ha az eredeti kiszolgáló online állapotba kerül, a Geo-visszaállítási adatbázisokhoz való felhasználói hozzáférés előkészítése bármikor elvégezhető (például a DR részletezés részeként).

> [!NOTE]
> Ha olyan kiszolgálóra végzi a feladatátvételt vagy a Geo-visszaállítást, amely nem rendelkezik megfelelően konfigurált bejelentkezésekkel, akkor a hozzáférése a kiszolgálói rendszergazdai fiókra korlátozódik.

A bejelentkezések a célkiszolgálón való beállítása az alábbi három lépést ismerteti:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Az elsődleges adatbázishoz hozzáféréssel rendelkező bejelentkezések meghatározása

A folyamat első lépése annak meghatározása, hogy mely bejelentkezéseket kell duplikálni a célkiszolgálón. Ez egy pár SELECT utasítással érhető el, egyet a forráskiszolgálón található logikai főadatbázisban, egyet pedig maga az elsődleges adatbázisban.

Csak a kiszolgáló rendszergazdája vagy a **loginmanager szerepkörhöz adhatja** -kiszolgáló szerepkör tagja határozhatja meg a forráskiszolgálón a következő SELECT utasítással rendelkező bejelentkezéseket.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Csak a db_owner adatbázis-szerepkör, a dbo-felhasználó vagy a kiszolgáló rendszergazdája határozható meg az összes adatbázis-felhasználói tag az elsődleges adatbázisban.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Az 1. lépésben azonosított bejelentkezések SID-azonosítójának megkeresése

Az előző szakaszban található lekérdezések kimenetének és a biztonsági azonosítók megfeleltetésének összehasonlításával leképezheti a kiszolgáló bejelentkezési adatait az adatbázis felhasználójának. A megfelelő biztonsági azonosítóval rendelkező adatbázis-felhasználóval rendelkező bejelentkezések felhasználói hozzáféréssel rendelkeznek az adatbázishoz, mint az adatbázis felhasználói rendszerbiztonsági tag.

A következő lekérdezéssel megtekintheti az összes felhasználói résztvevőt és azok SID-fájljait egy adatbázisban. Ezt a lekérdezést csak a db_owner adatbázis-szerepkör vagy a kiszolgáló rendszergazdája futtathatja.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> A **entitástulajdonos** és a **sys** felhasználók *Null* SID azonosítóval rendelkeznek, a **vendég** SID pedig **kell lennie 0x00**. A **dbo** SID a *0x01060000000001648000000000048454*-vel kezdődhet, ha az adatbázis létrehozója a kiszolgáló rendszergazdája volt a **DbManager**tagja helyett.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. A bejelentkezések létrehozása a célkiszolgálón

Utolsó lépésként nyissa meg a célkiszolgálóra vagy a kiszolgálókat, és a megfelelő biztonsági azonosítókkal létrehozza a bejelentkezéseket. Az alapszintű szintaxis a következő.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Ha a másodlagoshoz szeretné megadni a felhasználói hozzáférést, de az elsődlegeshez nem, az alábbi szintaxissal módosíthatja a felhasználói bejelentkezést az elsődleges kiszolgálón.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> A LETILTÁS nem módosítja a jelszót, így szükség esetén bármikor engedélyezheti.

## <a name="next-steps"></a>További lépések

* Az adatbázis-hozzáférés és bejelentkezések kezelésével kapcsolatos további információkért [lásd: SQL Database biztonság: Az adatbázis-hozzáférés és a](sql-database-manage-logins.md)bejelentkezési biztonság kezelése.
* További információ a tárolt adatbázis-felhasználókról: [tárolt adatbázis-felhasználók – az adatbázis hordozhatóvé tétele](https://msdn.microsoft.com/library/ff929188.aspx).
* Az aktív geo-replikációval kapcsolatos információkért lásd: [aktív földrajzi replikálás](sql-database-active-geo-replication.md).
* További információ az automatikus feladatátvételi csoportokról: [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md).
* További információ a Geo-visszaállítás használatáról: [geo-visszaállítás](sql-database-recovery-using-backups.md#geo-restore)
