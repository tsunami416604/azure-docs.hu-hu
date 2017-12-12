---
title: "Az Azure AD Connect: Hogyan tudja elhárítani az LocalDB 10 GB-os korlát probléma |} Microsoft Docs"
description: "Ez a témakör ismerteti az Azure AD Connect szinkronizálási szolgáltatás helyreállítása, ha 10 GB-os LocalDB probléma korlátozza."
services: active-directory
documentationcenter: 
author: cychua
manager: mtillman
editor: 
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: 7b54461a58fb6b60d0686743f90b6c85d7819f1f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Az Azure AD Connect: Hogyan tudja elhárítani az LocalDB 10 GB-os korlát
Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Használhatja az Azure AD Connecttel együtt telepített alapértelmezett SQL Server 2012 Express LocalDB-t, vagy használhatja saját teljes SQL-kiszolgálóját. Az SQL Server Express 10 GB-os méretkorláttal rendelkezik. Ha a LocalDB-t használja és eléri a korlátot, az Azure AD Connect szinkronizálási szolgáltatása többé nem indul majd el, vagy nem végzi el megfelelően a szinkronizálást. Ez a cikk ismerteti a helyreállítási lépéseket.

## <a name="symptoms"></a>Probléma
Két közös jelenségek van:

* Az Azure AD Connect szinkronizálási szolgáltatás **fut** , de nem tudja szinkronizálni *"leállt-adatbázis-lemez-teljes"* hiba.

* Az Azure AD Connect szinkronizálási szolgáltatás **nem indíthatók el**. Amikor megkísérli elindítani a szolgáltatást, az nem esemény 6323 és hibaüzenet *"a kiszolgáló hibába ütközött, mert nincs elég szabad lemezterület az SQL Server."*

## <a name="short-term-recovery-steps"></a>Rövid távú helyreállítási lépések
Ez a szakasz a lépéseit az Azure AD Connect szinkronizálási szolgáltatás folytatja a működését a szükséges adatbázis-terület felszabadítását. A lépések az alábbiak:
1. [A szinkronizálási szolgáltatás állapotának meghatározása](#determine-the-synchronization-service-status)
2. [Az adatbázis zsugorítása](#shrink-the-database)
3. [Futtassa az előzményadatok törlése](#delete-run-history-data)
4. [Rövidítse le a futtatási előzményei adatok megőrzési időtartama](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>A szinkronizálási szolgáltatás állapotának meghatározása
Először határozza meg, hogy a szinkronizálási szolgáltatás még fut vagy nem:

1. Jelentkezzen be rendszergazdaként az Azure AD Connect-kiszolgáló.

2. Ugrás a **szolgáltatásvezérlő**.

3. Tekintse meg a **Microsoft Azure AD Sync**.


4. Ha fut, ne állítsa le és indítsa újra a szolgáltatást. Kihagyás [az adatbázis zsugorítása](#shrink-the-database) . lépés:, és navigáljon [Delete futtatása előzményadatok](#delete-run-history-data) lépés.

5. Ha nem fut, próbálja meg elindítani a szolgáltatást. Kihagyása, ha a szolgáltatás sikeresen elindul, [az adatbázis zsugorítása](#shrink-the-database) . lépés:, és navigáljon [Delete futtatása előzményadatok](#delete-run-history-data) lépés. Ellenkező esetben folytassa [az adatbázis zsugorítása](#shrink-the-database) lépés.

### <a name="shrink-the-database"></a>Az adatbázis zsugorítása
A zsugorítási művelet használatával szabadítson fel elegendő DB helyet a szinkronizálási szolgáltatás elindításához. Az felszabadítja DB szóközök eltávolításával az adatbázisban. Ez a lépés esetén a legjobb, mert nem garantált, hogy mindig helyreállíthatja terület. További információ a zsugorítási műveletet, olvassa el ebben a cikkben [adatbázis zsugorítása](https://msdn.microsoft.com/library/ms189035.aspx).

> [!IMPORTANT]
> Kihagyhatja ezt a lépést, ha a szinkronizálási szolgáltatás futtatásához. Nem ajánlott elvégezni az SQL Database, ahogyan a megnövekedett töredezettsége miatt teljesítménycsökkenést eredményezhet.

Az Azure AD Connect létrehozott adatbázis neve nem **ADSync**. A zsugorítási művelet elvégzéséhez be kell jelentkeznie vagy a sysadmin (rendszergazda) vagy az adatbázis DBO. Az Azure AD Connect telepítése során a következő fiókok jogokat kapnak a sysadmin (rendszergazda):
* A helyi rendszergazdák
* Az Azure AD Connect telepítésének futtatásához használt felhasználói fiók.
* A szinkronizálási szolgáltatásfiók az Azure AD Connect szinkronizálási szolgáltatás üzemi környezetben használt.
* A helyi csoport telepítése során létrehozott ADSyncAdmins.

1. Készítsen biztonsági mentést az adatbázis másolása **ADSync.mdf** és **ADSync_log.ldf** alatt található fájlok `%ProgramFiles%\Microsoft Azure AD Sync\Data` egy biztonságos helyre.

2. Indítson el egy új PowerShell-munkamenetet.

3. Navigáljon a mappa `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn`.

4. Start **sqlcmd** segédprogram a parancs futtatásával `./SQLCMD.EXE -S “(localdb)\.\ADSync” -U <Username> -P <Password>`, az adatbázis DBO vagy egy rendszergazdai hitelesítő adatokkal.

5. Az adatbázis, az sqlcmd parancssorból: zsugorítani (1 >), adja meg `DBCC Shrinkdatabase(ADSync,1);`, utána pedig `GO` a következő sorban található.

6. Ha a művelet sikeres, próbálja meg újból elindítani a szinkronizálási szolgáltatást. Ha a szinkronizálási szolgáltatást el lehet indítani, folytassa a [Delete előzményadatok futtassa](#delete-run-history-data) lépés. Ha nem, forduljon a támogatási szolgálathoz.

### <a name="delete-run-history-data"></a>Futtassa az előzményadatok törlése
Alapértelmezés szerint az Azure AD Connect megőrzi be a hét nap értékelésével futtatási előzményei. Ebben a lépésben azt DB jusson, hogy az Azure AD Connect szinkronizálási szolgáltatás is indítsa el újra a szinkronizálással futtatási előzményei adatokat törli.

1.  Start **Synchronization Service Managert** START → szinkronizálási szolgáltatás címen.

2.  Lépjen a **műveletek** fülre.

3.  A **műveletek**, jelölje be **egyértelmű futtatása**...

4.  Választhat **törölje az összes futtatása** vagy **előtt fut törlése... <date>**  lehetőséget. Ajánlott elindításához futtassa a két napnál régebbi előzmények adatok törlésével. Ha továbbra is az adatbázis méretének problémát tapasztal, majd válassza ki a **törölje az összes futtatása** lehetőséget.

### <a name="shorten-retention-period-for-run-history-data"></a>Rövidítse le a futtatási előzményei adatok megőrzési időtartama
Ez a lépés akkor több szinkronizálási ciklust követően a 10 GB-os korlát probléma rendszert futtató valószínűségét csökkentése érdekében.

1. Nyisson meg egy új PowerShell-munkamenetet.

2. Futtatás `Get-ADSyncScheduler` és jegyezze fel a PurgeRunHistoryInterval tulajdonság, amely azt adja meg az aktuális megőrzési időszak.

3. Futtatás `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00` beállítani a megőrzési idő két nap. A megőrzési idő, szükség szerint módosítsa.

## <a name="long-term-solution--migrate-to-full-sql"></a>Hosszú távú megoldásokról – a teljes SQL áttelepítése
A probléma általában előzetes, hogy 10 GB-os adatbázisméret már nem elegendő az Azure AD Connect szinkronizálása a helyszíni Active Directory, az Azure AD. Ajánlott váltani az SQL server teljes verzióját használja. Az Azure AD Connect üzemelő példányának LocalDB adatbázisát nem cserélheti le közvetlenül az SQL teljes verziójának adatbázisára. Ehelyett egy új Azure AD Connect-kiszolgálót kell üzembe helyeznie az SQL teljes verziójával. Ajánlott a párhuzamos migrálás, amely során az (SQL-adatbázist tartalmazó) új Azure AD Connect-kiszolgálót átmeneti kiszolgálóként helyezi üzembe a (LocalDB-adatbázist tartalmazó) meglévő Azure AD Connect-kiszolgáló mellett. 
* A távoli SQL az Azure AD Connecttel történő konfigurálásáról tekintse meg a [Az Azure AD Connect egyéni telepítése](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-custom) című cikket.
* Az Azure AD Connect frissítésének párhuzamos migrálásáról tekintse meg az [Azure AD Connect: Frissítés egy előző verzióról a legújabbra](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#swing-migration) című cikket.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
