---
title: 'Az Azure AD Connect: Helyreállítás 10 GB-os korlátot probléma LocalDB annak |} A Microsoft Docs'
description: Ez a témakör ismerteti az Azure AD Connect szinkronizálási szolgáltatás helyreállítása, ha a LocalDB 10 GB-os probléma korlátozza.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 410559ab03f0e0be71f2eba27ed71c9f7cf05862
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238539"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Az Azure AD Connect: Hogyan helyreállítás 10-GB ra korlátozott localdb adatbázisból
Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Használhatja az Azure AD Connecttel együtt telepített alapértelmezett SQL Server 2012 Express LocalDB-t, vagy használhatja saját teljes SQL-kiszolgálóját. Az SQL Server Express 10 GB-os méretkorláttal rendelkezik. Ha a LocalDB-t használja és eléri a korlátot, az Azure AD Connect szinkronizálási szolgáltatása többé nem indul majd el, vagy nem végzi el megfelelően a szinkronizálást. Ez a cikk ismerteti a helyreállítási lépéseket.

## <a name="symptoms"></a>Probléma
Nincsenek két gyakori jelenség:

* Az Azure AD Connect szinkronizálási szolgáltatás **fut-e** szinkronizálása meghiúsul, de *"stopped-adatbázis-lemez – teljes"* hiba.

* Az Azure AD Connect szinkronizálási szolgáltatás **nem tudja elindítani**. Amikor megkísérli elindítani a szolgáltatást, az esemény 6323 és a hibaüzenet sikertelen *"a kiszolgáló hibába ütközött, mert nincs elég szabad lemezterület, SQL Server."*

## <a name="short-term-recovery-steps"></a>Rövid távú helyreállítási lépések
Ez a szakasz ismerteti az Azure AD Connect szinkronizálási szolgáltatás művelet folytatásához szükséges DB hely felszabadítása érdekében. A lépések a következők:
1. [A szinkronizálási szolgáltatás állapotának meghatározása](#determine-the-synchronization-service-status)
2. [Az adatbázis zsugorítása](#shrink-the-database)
3. [Futtatási előzményadatokat DELETE](#delete-run-history-data)
4. [Rövidítse le a futtatási előzmények adatok megőrzési ideje](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>A szinkronizálási szolgáltatás állapotának meghatározása
Először határozza meg, a szinkronizálási szolgáltatás továbbra is fut-e vagy sem:

1. Jelentkezzen be rendszergazdaként az Azure AD Connect-kiszolgáló.

2. Lépjen a **szolgáltatásvezérlő**.

3. Állapotának ellenőrzéséhez **a Microsoft Azure AD Sync**.


4. Ha fut, ne állítsa le és indítsa újra a szolgáltatást. Kihagyás [csökkentheti az adatbázis](#shrink-the-database) lépést és folytassa [Delete futtatási előzményadatokat](#delete-run-history-data) . lépés.

5. Ha nem fut, próbálja meg elindítani a szolgáltatást. Ha a szolgáltatás sikeresen elindul, hagyja ki [csökkentheti az adatbázis](#shrink-the-database) lépést és folytassa [Delete futtatási előzményadatokat](#delete-run-history-data) . lépés. Ellenkező esetben folytassa [csökkentheti az adatbázis](#shrink-the-database) . lépés.

### <a name="shrink-the-database"></a>Az adatbázis zsugorítása
A zsugorítási művelet használatával szabadítson fel elegendő helyet a szinkronizálási szolgáltatás elindításához. Azt szabadít fel a helyet, az adatbázis nem állhat kizárólag szóközökből eltávolításával. Ez a lépés nem a legjobb, nem garantált, hogy helyet mindig állíthatja helyre. További információ a zsugorítási műveletet, olvassa el ebben a cikkben [adatbázis zsugorítani](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Hagyja ki ezt a lépést, ha a szinkronizálási szolgáltatás futtatásához kap. Az SQL DB igazodjon vezethet, gyenge teljesítményt, nagyobb töredezettség miatt nem ajánlott.

Az Azure AD Connect létrehozott adatbázis neve nem **ADSync**. Zsugorítási művelet végrehajtására, vagy a sysadmin (rendszergazda) vagy az adatbázis Tulajdonosának nevével, be kell jelentkeznie. Az Azure AD Connect telepítése során a következő fiókok jogokat kapnak a sysadmin (rendszergazda):
* A helyi rendszergazdák
* Az Azure AD Connect telepítés futtatásához használt felhasználói fiók.
* A szinkronizálási szolgáltatás fiók, amely az Azure AD Connect szinkronizálási szolgáltatás működési kontextusában.
* A helyi csoport a telepítés során létrehozott ADSyncAdmins.

1. Készítsen biztonsági másolatot az adatbázis másolásával **ADSync.mdf** és **ADSync_log.ldf** alatt található fájlok `%ProgramFiles%\Microsoft Azure AD Sync\Data` egy biztonságos helyre.

2. Indítsa el egy új PowerShell-munkamenetet.

3. Lépjen a mappába `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Indítsa el **sqlcmd** segédprogram a parancs futtatásával `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>`, a sysadmin (rendszergazda) vagy az adatbázis DBO a hitelesítő adatok használatával.

5. Az adatbázis az Sqlcmd parancsot a parancssorba zsugorítása (1 >), adja meg `DBCC Shrinkdatabase(ADSync,1);`, utána pedig `GO` a következő sorban.

6. Ha a művelet sikeres, próbálja meg újból elindítani a szinkronizálási szolgáltatás. Ha a szinkronizálási szolgáltatás elindításához lépjen a [Delete futtatási előzményadatokat](#delete-run-history-data) . lépés. Ha nem, forduljon az ügyfélszolgálathoz.

### <a name="delete-run-history-data"></a>Futtatási előzményadatokat DELETE
Alapértelmezés szerint az Azure AD Connect megőrzi be a hét napnyi futtatási előzményadatok. Ebben a lépésben töröljük a futtatási előzményadatok DB jusson, hogy az Azure AD Connect szinkronizálási szolgáltatás segítségével indítsa el újra a szinkronizálással.

1.  Indítsa el **Synchronization Service Managert** a KEZDŐ → szinkronizálási szolgáltatás.

2.  Nyissa meg a **műveletek** fülre.

3.  A **műveletek**válassza **egyértelmű futtatások**...

4.  Választhat **törölje minden Futtatás** vagy **Clear futtatása előtt... <date>**  lehetőséget. Ajánlott kezdeni a futtatási előzményadatokat, amely két napnál régebbi törlése. Ha történő adatbázis méretét a probléma továbbra is, majd válassza ki a **törölje minden Futtatás** lehetőséget.

### <a name="shorten-retention-period-for-run-history-data"></a>Rövidítse le a futtatási előzmények adatok megőrzési ideje
Ez a lépés nem több szinkronizálási ciklus után a 10 GB-os korlátot probléma felmerülése valószínűségét csökkentése érdekében.

1. Nyisson meg egy új PowerShell-munkamenetet.

2. Futtatás `Get-ADSyncScheduler` , és jegyezze fel a PurgeRunHistoryInterval tulajdonság, amely megadja az aktuális megőrzési időszak.

3. Futtatás `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` két nappal a megőrzési időszak beállítása. A megőrzési időtartam, szükség szerint módosítsa.

## <a name="long-term-solution--migrate-to-full-sql"></a>Hosszú távú megoldás – a teljes funkciókészletű SQL áttelepítése
A probléma általában előzetes, hogy 10 GB-os adatbázisméret már nem elegendő az Azure AD Connect szinkronizálja a helyszíni Active Directory, az Azure AD-e. Azt Javasoltjuk, váltson át az SQL server teljes verzióját használja. Az Azure AD Connect üzemelő példányának LocalDB adatbázisát nem cserélheti le közvetlenül az SQL teljes verziójának adatbázisára. Ehelyett egy új Azure AD Connect-kiszolgálót kell üzembe helyeznie az SQL teljes verziójával. Ajánlott a párhuzamos migrálás, amely során az (SQL-adatbázist tartalmazó) új Azure AD Connect-kiszolgálót átmeneti kiszolgálóként helyezi üzembe a (LocalDB-adatbázist tartalmazó) meglévő Azure AD Connect-kiszolgáló mellett. 
* A távoli SQL az Azure AD Connecttel történő konfigurálásáról tekintse meg a [Az Azure AD Connect egyéni telepítése](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom) című cikket.
* Az Azure AD Connect frissítésének párhuzamos migrálásáról tekintse meg az [Azure AD Connect: Frissítés egy előző verzióról a legújabbra](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) című cikket.

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
