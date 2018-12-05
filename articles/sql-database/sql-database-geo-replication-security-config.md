---
title: Vészhelyreállítás az Azure SQL Database biztonságának konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogy a biztonsági szempontok konfigurálásához és kezeléséhez biztonsági után egy adatbázis visszaállítása vagy feladatátvétel a másodlagos kiszolgálóra.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: ac57f97f45f14c5011782fa0fb8b708bc52bd151
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871183"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurálhatja és kezelheti az Azure SQL Database geo-visszaállítás vagy a feladatátvételi biztonsága 

Ez a témakör ismerteti a hitelesítési követelmények konfigurálása és [aktív georeplikáció](sql-database-geo-replication-overview.md) és a felhasználói hozzáférés a másodlagos adatbázis beállításához szükséges lépéseket. Azt is ismerteti, hogyan lehet a helyreállított adatbázis elérésének lehetővé tétele a forgatókönyv használata útán [geo-visszaállítás](sql-database-recovery-using-backups.md#geo-restore). A helyreállítási lehetőségek további információkért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

> [!NOTE]
> [Aktív georeplikáció](sql-database-geo-replication-overview.md) már elérhető az összes adatbázisokhoz minden szolgáltatásszinten.
>  

## <a name="disaster-recovery-with-contained-users"></a>Vész-helyreállítási a tartalmazott felhasználók
Ellentétben a hagyományos felhasználóknak, amelyet le kell képezni a bejelentkezéseket a master adatbázisban, egy felhasználó teljesen által felügyelt maga az adatbázis. Ez a két előnye van. A vészhelyreállítás esetén a felhasználók továbbra is az új elsődleges adatbázishoz való csatlakozáshoz, vagy az adatbázist helyre az geo-visszaállítás, további konfiguráció nélkül használatával, mert az adatbázis a felhasználókat kezeli. Is találhatók esetleges méretezhetőséget és jobb teljesítményt nyújt az ebben a konfigurációban a bejelentkezési szempontjából. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://msdn.microsoft.com/library/ff929188.aspx) cikkben talál. 

A fő kompromisszum, hogy a vész-helyreállítást, ipari méretekben kezelése nehéz lehet. Ha több adatbázist használó, ugyanazokat a bejelentkezési adatokat, a hitelesítő adatok használatával tartalmazott felhasználók több adatbázis karbantartása előfordulhat, hogy negálandó tartalmazott felhasználók előnyeit. Például a jelszóházirend Elforgatás van szükség, hogy módosításokat konzisztens módon több adatbázisban ahelyett, hogy a bejelentkezés után a master adatbázisban a jelszavának módosítása. Ebből kifolyólag több adatbázist használó ugyanazt a felhasználónevet és jelszót, ha tartalmazott felhasználók használata nem ajánlott. 

## <a name="how-to-configure-logins-and-users"></a>Bejelentkezések és felhasználók konfigurálása
Bejelentkezések és felhasználók használata (ahelyett, hogy tartalmazott felhasználók), akkor győződjön meg arról, hogy létezik-e az azonos bejelentkezéseket a master adatbázisban, további lépéseket kell végrehajtani. Az alábbi szakaszok a lépések részt vevő és a további szempontok tagolni.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Állítsa be a felhasználói hozzáférés a másodlagos és a helyreállított adatbázis
Ahhoz, hogy a másodlagos adatbázis csak olvasható másodlagos adatbázis használhatóvá válik, és az új elsődleges adatbázis vagy az adatbázis helyreállítása geo-visszaállítás használatával való megfelelő hozzáférés biztosításához a master adatbázisban a célkiszolgáló rendelkeznie kell a megfelelő biztonsági a helyreállítás előtt a helyükön konfiguráció.

Egyes lépéseihez szükséges engedélyeket a témakör későbbi része ismerteti.

Felhasználói hozzáférés a georeplikáció másodlagos előkészítése georeplikáció konfigurálása részeként kell végrehajtani. Geo-visszaállított adatbázishoz a felhasználói hozzáférés előkészítése kell hajtható végre, tetszőleges időpontban, amikor az eredeti kiszolgáló online állapotban (például: a Dr működési részeként).

> [!NOTE]
> Ha átadja a feladatokat, vagy a geo-visszaállítás, vagyis a megfelelően konfigurált bejelentkezéseket, hozzáféréssel nem rendelkező kiszolgálóra kell korlátozni a kiszolgálói rendszergazdai fiókkal.
> 
> 

Bejelentkezések a célkiszolgálón beállítása magában foglalja az alábbi három lépést:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Határozza meg az elsődleges adatbázishoz hozzáféréssel rendelkező bejelentkezések:
A folyamat első lépéseként határozza meg, mely bejelentkezéseknek kell készíteni a célkiszolgálón. Mindez egy pár SELECT utasításokkal, egy, a logikai master adatbázis a forráskiszolgálón és a egy, az elsődleges adatbázis magát.

Csak a kiszolgáló rendszergazdája vagy tagja a **LoginManager** kiszolgálói szerepkör a forráskiszolgálón az alábbi utasítást a bejelentkezések meghatározásához. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Csak a db_owner database szerepkör, a dbo-felhasználó vagy a kiszolgáló-rendszergazdai tagja megadhatja, hogy mind az elsődleges adatbázis az adatbázis felhasználói a rendszerbiztonsági tagok közül.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. A bejelentkezések, az 1. lépésben azonosított tartozó SID kereséséhez:
Az előző szakaszban a lekérdezések kimenetének összehasonlításával, és a biztonsági azonosítók egyeztetése, leképezheti a kiszolgálói bejelentkezésen adatbázis-felhasználót. Bejelentkezések, amelyek rendelkeznek a megfelelő SID-AZONOSÍTÓVAL rendelkező adatbázis-felhasználója, a fő adatbázis-felhasználót, hogy az adatbázis felhasználói jogosultságra van. 

Az alábbi lekérdezés használható az összes olyan egyszerű felhasználói névnek és a egy adatbázisban a SID-k megtekintéséhez. Csak a db_owner database szerepkör- vagy admin tagjaként futtathatja ezt a lekérdezést.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> A **INFORMATION_SCHEMA** és **sys** felhasználók *NULL* SID-k, és a **vendég** SID **0x00**. A **dbo** SID kezdődhet *0x01060000000001648000000000048454*, ha az adatbázis-készítő a kiszolgálói rendszergazda helyett tagja volt **DbManager**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. A bejelentkezések létrehozása a célkiszolgálón:
Az utolsó lépés, hogy nyissa meg a célkiszolgáló vagy kiszolgálókon, és a megfelelő biztonsági azonosítókkal való létrehozásához a bejelentkezések létrehozása. Az alapvető szintaxisa a következő.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Ha azt szeretné, a másodlagos, de nem az elsődleges felhasználói hozzáférést, akkor ehhez módosítását a felhasználói bejelentkezési az elsődleges kiszolgálón a következő szintaxis használatával.
> 
> ALTER LOGIN <login name> LETILTÁSA
> 
> Tiltsa le a jelszót, és nem változik, így mindig engedélyezheti, ha szükséges.
> 
> 

## <a name="next-steps"></a>További lépések
* Az adatbázis-hozzáférés és bejelentkezések kezelése további információkért lásd: [SQL Database biztonsága: adatbázis-hozzáférés és a bejelentkezési biztonság felügyelete](sql-database-manage-logins.md).
* A tartalmazott adatbázis felhasználóit további információkért lásd: [tartalmazott adatbázis-felhasználók – az adatbázis hordozhatóvá](https://msdn.microsoft.com/library/ff929188.aspx).
* További információ a használatáról és konfigurálásáról az aktív georeplikáció: [aktív georeplikáció](sql-database-geo-replication-overview.md)
* A geo-visszaállítás használatával kapcsolatos információkért lásd: [geo-visszaállítás](sql-database-recovery-using-backups.md#geo-restore)

