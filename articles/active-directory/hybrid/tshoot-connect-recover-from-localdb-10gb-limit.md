---
title: 'Azure AD Connect: A LocalDB 10 GB-os limitproblémája helyreállítása | Microsoft dokumentumok'
description: Ez a témakör azt ismerteti, hogyan állíthatja helyre az Azure AD Connect szinkronizálási szolgáltatást, ha LocalDB 10GB korláttal kapcsolatos problémával találkozik.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d420c64c5834f7d3cb11d2f5f59e3ed85a54891
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386924"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: Helyreállítás 10 GB-ra korlátozott LocalDB adatbázisból
Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Használhatja az Azure AD Connecttel együtt telepített alapértelmezett SQL Server 2012 Express LocalDB-t, vagy használhatja saját teljes SQL-kiszolgálóját. Az SQL Server Express 10 GB-os méretkorláttal rendelkezik. Ha a LocalDB-t használja és eléri a korlátot, az Azure AD Connect szinkronizálási szolgáltatása többé nem indul majd el, vagy nem végzi el megfelelően a szinkronizálást. Ez a cikk a helyreállítási lépéseket tartalmazza.

## <a name="symptoms"></a>Probléma
Két gyakori tünet:

* Az Azure AD Connect szinkronizálási szolgáltatás **fut,** de nem szinkronizálja a *"stopped-database-disk-full"* hiba.

* Az Azure AD Connect szinkronizálási **szolgáltatása nem indítható el.** Amikor megpróbálja elindítani a szolgáltatást, az sikertelen lesz a 6323-as esemény és *a következő hibaüzenettel: "A kiszolgáló hibát észlelt, mert az SQL Server lemezterületből nincs elég lemez."*

## <a name="short-term-recovery-steps"></a>Rövid távú helyreállítási lépések
Ez a szakasz az Azure AD Connect szinkronizálási szolgáltatás működésének folytatásához szükséges adatbázis-terület visszaszerzésének lépéseit ismerteti. A lépések a következők:
1. [A szinkronizálási szolgáltatás állapotának meghatározása](#determine-the-synchronization-service-status)
2. [Az adatbázis zsugorítása](#shrink-the-database)
3. [Futtatási előzmények adatainak törlése](#delete-run-history-data)
4. [A futtatási előzmények adatainak megőrzési időszakának lerövidítése](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>A szinkronizálási szolgáltatás állapotának meghatározása
Először határozza meg, hogy a szinkronizálási szolgáltatás továbbra is fut-e vagy sem:

1. Jelentkezzen be az Azure AD Connect-kiszolgálóra rendszergazdaként.

2. Nyissa meg a **Szolgáltatásvezérlés-kezelőt.**

3. Ellenőrizze a **Microsoft Azure AD Sync**állapotát.


4. Ha fut, ne állítsa le vagy indítsa újra a szolgáltatást. Ugrás [az Adatbázis zsugorítása](#shrink-the-database) lépésre, és ugorjon a [Futtatási előzmények adatainak törlése](#delete-run-history-data) lépésre.

5. Ha nem fut, próbálja meg elindítani a szolgáltatást. Ha a szolgáltatás sikeresen elindul, hagyja ki [az Adatbázis zsugorítása](#shrink-the-database) lépést, és lépjen a [Futtatási előzmények adatainak törlése](#delete-run-history-data) lépésre. Ellenkező esetben folytassa [az Adatbázis zsugorítása](#shrink-the-database) lépéssel.

### <a name="shrink-the-database"></a>Az adatbázis zsugorítása
A Zsugorítás művelettel elegendő adatbázis-helyet szabadíthat fel a szinkronizálási szolgáltatás elindításához. Az adatbázis ban lévő szóközök eltávolításával adatbázis-adatbázishelyet szabadít fel. Ez a lépés a legjobb erőfeszítés, mivel nem garantált, hogy mindig helyretudja állítani a helyet. Ha többet szeretne megtudni a Zsugorítás műveletről, olvassa el ezt a cikket [Adatbázis összeadása](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Hagyja ki ezt a lépést, ha le tudja futtatni a szinkronizálási szolgáltatást. Nem ajánlott az SQL DB zsugorítása, mivel a megnövekedett töredezettség miatt gyenge teljesítményhez vezethet.

Az Azure AD Connect számára létrehozott adatbázis neve **ADSync**. Zsugorítási művelet végrehajtásához az adatbázis rendszergazdaként vagy DBO-jaként kell bejelentkeznie. Az Azure AD Connect telepítése során a következő fiókok rendszergazdai jogokat kapnak:
* Helyi rendszergazdák
* Az Azure AD Connect telepítésének futtatásához használt felhasználói fiók.
* Az Azure AD Connect szinkronizálási szinkronizálási szolgáltatás működési környezetében használt szinkronizálási szolgáltatás.
* A telepítés során létrehozott ADSyncAdmins helyi csoport.

1. Készítsen biztonsági másolatot az adatbázisról úgy, `%ProgramFiles%\Microsoft Azure AD Sync\Data` hogy az **ADSync.mdf** és **ADSync_log.ldf** fájlokat biztonságos helyre másolja.

2. Új PowerShell-munkamenet indítása.

3. Keresse meg `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`a mappát .

4. Indítsa el az **sqlcmd** segédprogramot a parancs `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`futtatásával, egy rendszergazda vagy az adatbázis DBO hitelesítő adataival.

5. Az adatbázis zsugorításához írja be `DBCC Shrinkdatabase(ADSync,1);`az sqlcmd-üzenetbe (1>), majd `GO` a következő sorban.

6. Ha a művelet sikeres, próbálja meg újra elindítani a szinkronizálási szolgáltatást. Ha el tudja indítani a Szinkronizálási szolgáltatást, nyissa meg [a Futtatási előzmények adatainak törlése](#delete-run-history-data) című lépést. Ha nem, forduljon az ügyfélszolgálathoz.

### <a name="delete-run-history-data"></a>Futtatási előzmények adatainak törlése
Alapértelmezés szerint az Azure AD Connect legfeljebb hét napi futtatási előzményadatokat őriz meg. Ebben a lépésben töröljük a futtatási előzmények adatait, hogy visszaszerezzük a DB-területet, hogy az Azure AD Connect szinkronizálási szolgáltatás újra elkezdhesse a szinkronizálást.

1. Indítsa el **a Szinkronizálási szolgáltatáskezelőt** a START → Szinkronizálási szolgáltatás sal.

2. Nyissa meg a **Műveletek** lapot.

3. A **Műveletek csoportban**válassza a **Futtatások törlése**lehetőséget ...

4. Választhatja **az Összes futtatás törlése** vagy a Clear **futások törlése lehetőséget... dátum \<>** opciót. Javasoljuk, hogy kezdje a két napnál régebbi futtatási előzményadatok törlésével. Ha továbbra is az adatbázis méretével kapcsolatos probléma lép be, válassza az **Összes futtatás törlése** lehetőséget.

### <a name="shorten-retention-period-for-run-history-data"></a>A futtatási előzmények adatainak megőrzési időszakának lerövidítése
Ez a lépés csökkenti annak valószínűségét, hogy több szinkronizálási ciklus után a 10 GB-os korlát problémába fut.

1. Nyisson meg egy új PowerShell-munkamenetet.

2. Futtassa `Get-ADSyncScheduler` és vegye figyelembe a PurgeRunHistoryInterval tulajdonságot, amely megadja az aktuális megőrzési időszakot.

3. Futtassa `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` a megőrzési időszak két napra való beállításához. Szükség szerint módosítsa a megőrzési időt.

## <a name="long-term-solution--migrate-to-full-sql"></a>Hosszú távú megoldás – Migrálás teljes SQL-re
A probléma általában azt jelzi, hogy a 10 GB-os adatbázis mérete már nem elegendő az Azure AD Connect a helyszíni Active Directory és az Azure AD szinkronizálásához. Javasoljuk, hogy az SQL-kiszolgáló teljes verziójának használatára váltson. Az Azure AD Connect üzemelő példányának LocalDB adatbázisát nem cserélheti le közvetlenül az SQL teljes verziójának adatbázisára. Ehelyett egy új Azure AD Connect-kiszolgálót kell üzembe helyeznie az SQL teljes verziójával. Ajánlott a párhuzamos migrálás, amely során az (SQL-adatbázist tartalmazó) új Azure AD Connect-kiszolgálót átmeneti kiszolgálóként helyezi üzembe a (LocalDB-adatbázist tartalmazó) meglévő Azure AD Connect-kiszolgáló mellett. 
* A távoli SQL az Azure AD Connecttel történő konfigurálásáról tekintse meg a [Az Azure AD Connect egyéni telepítése](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom) című cikket.
* Az Azure AD Connect frissítésének párhuzamos migrálásáról tekintse meg az [Azure AD Connect: Frissítés egy előző verzióról a legújabbra](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) című cikket.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
