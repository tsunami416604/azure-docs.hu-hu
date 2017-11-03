---
title: "Az Azure SQL Database biztonsági vész-helyreállítási konfigurálása |} Microsoft Docs"
description: "Ismerje meg a biztonsági szempontok konfigurálásához és kezeléséhez biztonsági egy adatbázis visszaállítása vagy egy másodlagos kiszolgáló a feladatátvétel után."
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: c7c898c9-69d4-4e16-8b7e-720bbb3353dd
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/13/2016
ms.author: sashan
ms.openlocfilehash: 5bdcdba4956a12b54559b8accf822a4f41656045
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurálhatja és kezelheti az Azure SQL Database biztonsági georedundáns helyreállítás vagy feladatátvételi 

Ez a témakör ismerteti a hitelesítési követelmények konfigurálása és [aktív georeplikáció](sql-database-geo-replication-overview.md) és a felhasználó hozzáférését a másodlagos adatbázis beállításához szükséges lépéseket. Is bemutatja, hogyan lehet, hogy hozzáférhessenek a helyreállított adatbázis használata után [georedundáns helyreállítás](sql-database-recovery-using-backups.md#geo-restore). Helyreállítási lehetőségekről további információkért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).

> [!NOTE]
> [Aktív georeplikáció](sql-database-geo-replication-overview.md) érhető el az összes olyan adatbázis összes szolgáltatásrétegeiben használt funkciókkal.
>  

## <a name="disaster-recovery-with-contained-users"></a>A tartalmazott felhasználók katasztrófa utáni helyreállítás
Ellentétben a hagyományos felhasználóknak, amelyet le kell képezni a master adatbázisban bejelentkezéseket, egy felhasználó teljesen által felügyelt maga az adatbázis. Azt két előnnyel jár. A vészhelyreállítás esetén a felhasználók továbbra is az új elsődleges adatbázishoz való kapcsolódáshoz vagy az adatbázist helyre az georedundáns helyreállítás, további konfiguráció nélkül használja, mert az adatbázis kezeli a felhasználók. Nincsenek is lehetséges méretezhetőségét és teljesítményét számos előnyt biztosít az ebben a konfigurációban egy bejelentkezési szempontjából. További információt a [tartalmazottadatbázis-felhasználókkal kapcsolatos, az adatbázis hordozhatóvá tételével foglalkozó](https://msdn.microsoft.com/library/ff929188.aspx) cikkben talál. 

A fő kompromisszum, annál nagyobb kihívás a vész-helyreállítási folyamat léptékű kezelésére. Ha egyszerre több adatbázis ugyanazon bejelentkezési használó, a hitelesítő adatokat tartalmazott felhasználók több adatbázisok karbantartása előfordulhat, hogy semlegesítsék a tartalmazott felhasználók előnyeit. Például a jelszóházirend elforgatási van szükség, hogy lehet változtatás következetesen több adatbázis ahelyett, hogy megváltoztatta a jelszavát, a bejelentkezési azonosítóhoz egyszer a master adatbázisban. Ezért ha több, ugyanazt a felhasználónevet és jelszót, használó adatbázisok tartalmazott felhasználók használata nem ajánlott. 

## <a name="how-to-configure-logins-and-users"></a>Bejelentkezések és a felhasználók konfigurálása
Bejelentkezések és felhasználók használata (helyett a benne lévő felhasználók), annak érdekében, hogy létezik-e az azonos bejelentkezések a master adatbázisban további lépéseket kell végrehajtania. Az alábbi szakaszok felsorolják a lépéseket érintett és további szempontokat.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Állítsa be a másodlagos, és a helyreállított adatbázis felhasználói hozzáférése
Ahhoz, hogy a másodlagos adatbázis csak olvasható másodlagos adatbázis használható, és megfelelő hozzáféréssel az új elsődleges adatbázis vagy az adatbázis visszaállítással földrajzi helyre a célkiszolgáló a master adatbázis konfigurációval kell rendelkeznie a megfelelő biztonsági a helyreállítás előtt a helyükön.

A témakör későbbi részében egyes lépéseihez szükséges engedélyeket ismerteti.

Felkészülés a felhasználói hozzáférés a georeplikáció másodlagos georeplikáció konfigurálása során kell elvégezni. Felkészülés a földrajzi vissza adatbázisok felhasználói hozzáférést kell hajtható végre, tetszőleges időpontban, amikor az eredeti kiszolgáló online állapotban-e (pl. a vész-Helyreállítási részletezési részeként).

> [!NOTE]
> Ha a rendszer átadja, vagy olyan kiszolgálóra, amely nem rendelkezik hozzáféréssel a megfelelően konfigurált bejelentkezések georedundáns helyreállítás korlátozódik a kiszolgáló-rendszergazdai fiókot.
> 
> 

Az alábbiakban leírt három lépést beállítása a célkiszolgálón bejelentkezések foglal magában:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Határozza meg az elsődleges adatbázis eléréséhez felmérhetők:
A folyamat az első lépés annak meghatározásához, hogy mely bejelentkezések kell készíteni a cél kiszolgálón. Ez KIVÁLASZTÓ utasítást, az a logikai master adatbázis a forráskiszolgálón és a maga az elsődleges adatbázis két érhető el.

Csak a kiszolgáló rendszergazdája vagy egy másik a **LoginManager** kiszolgálói szerepkör megállapíthatja, hogy a bejelentkezési adatok, a forráskiszolgálón a következő SELECT utasítással. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Csak a db_owner adatbázis-szerepkör, a dbo felhasználói vagy kiszolgálói rendszergazda, tag lehet meghatározni az összes, az adatbázis-felhasználó rendszerbiztonsági tagot az elsődleges adatbázis.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. 1. lépésben azonosított bejelentkezések tartozó SID kereséséhez:
A fenti szakaszban leírt lekérdezések kimenetének összehasonlítása, és a biztonsági azonosítók megfelelő, a kiszolgálói bejelentkezési adatbázis-felhasználó is leképezheti. Egy adatbázis-felhasználó a megfelelő SID-AZONOSÍTÓVAL rendelkező felhasználók hozzáférhetnek felhasználó ahhoz az adatbázishoz adott adatbázis elsődleges felhasználóként. 

A következő lekérdezés segítségével láthatja az összes, a felhasználó rendszerbiztonsági tagok és az SID-adatbázisban. Csak a db_owner adatbázis-szerepkör- vagy felügyeleti tagjai futtathatják a lekérdezés.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> A **entitástulajdonos** és **sys** felhasználóknál *NULL* SID-k, és a **vendég** SID **0x00**. A **dbo** SID kezdődik *0x01060000000001648000000000048454*, ha az adatbázis-készítő tagja helyett a kiszolgáló rendszergazdája **DbManager**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. A bejelentkezési adatok létrehozása a célkiszolgálón:
Az utolsó lépése, hogy nyissa meg a célként megadott kiszolgáló vagy kiszolgálók, és a megfelelő SID-k a felmérhetők készítése. Az alapvető szintaxisa a következő.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Ha meg szeretné adni a felhasználói hozzáférést a másodlagos, de nem az elsődleges, akkor a következő szintaxis használatával az elsődleges kiszolgálón a felhasználói bejelentkezési módosításával teheti meg.
> 
> ALTER LOGIN <login name> LETILTÁSA
> 
> Tiltsa le a jelszó nem változik, ezért mindig engedélyezheti azt szükség esetén.
> 
> 

## <a name="next-steps"></a>Következő lépések
* Az adatbázis-hozzáférési és bejelentkezések kezelése további információkért lásd: [SQL-adatbázis biztonsági: adatbázis-hozzáférési és bejelentkezési biztonság kezeléséhez](sql-database-manage-logins.md).
* A tartalmazott adatbázis-felhasználók további információkért lásd: [tartalmazott adatbázis-felhasználók – így az adatbázis hordozható](https://msdn.microsoft.com/library/ff929188.aspx).
* Aktív georeplikáció konfigurálásával kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md)
* Georedundáns helyreállítás használatával kapcsolatos információkért lásd: [georedundáns helyreállítás](sql-database-recovery-using-backups.md#geo-restore)

