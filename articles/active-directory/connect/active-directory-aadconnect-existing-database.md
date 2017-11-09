---
title: "Az Azure AD Connect telepítése meglévő ADSync adatbázis segítségével |} Microsoft Docs"
description: "Ez a témakör ismerteti, hogyan használhatja a meglévő ADSync adatbázis."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.reviewer: cychua
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: billmath
ms.openlocfilehash: 61652d97429336dad23ba14f7349e27bf52d33d7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Az ADSync adatbázis Azure AD Connect telepítése
Az Azure AD Connect SQL Server adatbázis adatainak tárolásához szükséges. Használja az alapértelmezett SQL Server 2012 Express LocalDB az Azure AD Connect telepítve, vagy használja a saját SQL teljes verzióját. Korábban az Azure AD Connect telepítése során az ADSync nevű új adatbázis mindig létrejött. Az Azure AD Connect 1.1.613.0 verziót (vagy után) lehetősége van az Azure AD Connect telepítése mutat, azt a meglévő ADSync adatbázis.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Az ADSync adatbázis előnyei
Meglévő ADSync adatbázis mutat:

- Hitelesítő adatok, kivéve az ADSync adatbázis (beleértve az egyéni szinkronizálási szabályait, összekötők, szűrési és választható funkciók konfigurációjának) tárolja a szinkronizálási konfiguráció automatikusan helyre, és telepítés során használt . Szinkronizálni a módosításokat az Azure AD Connect által használt hitelesítő adatokat a helyszíni AD és az Azure AD vannak titkosítva, és csak érhető el az előző az Azure AD Connect-kiszolgáló.
- A azonosító adatok (összekötőterek és metaverzum társított) és az ADSync adatbázis tárolt szinkronizálási cookie-k is helyre. Az újonnan telepített az Azure AD Connect kiszolgáló továbbra is, ahol a korábbi Azure AD Connect-kiszolgáló abbahagyta, ahelyett, hogy a teljes szinkronizálás végrehajtásához szükség szinkronizálás.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Forgatókönyvekben, ahol az ADSync adatbázis előnyös
Ilyen előnyt hasznosak a következő esetekben:


- Rendelkezik egy meglévő Azure AD Connect telepítését. A meglévő Azure AD Connect-kiszolgáló már nem működik, de továbbra is működik-e az SQL server, amely tartalmazza az ADSync adatbázis. Új Azure AD Connect kiszolgáló telepítése, és mutasson a a meglévő ADSync adatbázis. 
- Rendelkezik egy meglévő Azure AD Connect telepítését. Az SQL server, amely tartalmazza az ADSync adatbázis már nem működik. Azonban, hogy a legutóbbi biztonsági mentése az adatbázis. Egy új SQL server először visszaállíthatja az ADSync adatbázis. Mely után egy új Azure AD Connect kiszolgáló telepítése, és mutasson a a visszaállított ADSync adatbázis.
- Rendelkezik egy meglévő Azure AD Connect-telepítés által használt localdb programba. A 10 GB-os korlát LocalDB elő, mert szeretné áttelepíteni a teljes SQL. Biztonsági mentés az ADSync adatbázis LocalDB, és annak visszaállítására egy SQL server. Mely után újra kell telepítenie egy új Azure AD Connect-kiszolgáló, és mutasson a a visszaállított ADSync adatbázis.
- Próbálja meg állítson be egy átmeneti kiszolgálón, és ellenőrizze, hogy a konfigurációja megfelelő-e az aktuális active server szeretné. Az ADSync adatbázis biztonsági mentése, és annak visszaállítására egy másik SQL server. Mely után újra kell telepítenie egy új Azure AD Connect-kiszolgáló, és mutasson a a visszaállított ADSync adatbázis.

## <a name="prerequisite-information"></a>Előfeltételként szolgáló információk

Fontos megjegyzések érvénybe vegye figyelembe a folytatás előtt:

- Mindenképpen tekintse át a szükséges előfeltételek telepítése az Azure AD Connect: hardver és előfeltételek, és a fiók és az Azure AD Connect telepítése szükséges engedélyek. A "meglévő adatbázis használata" mód segítségével az Azure AD Connect telepítése szükséges jogosultságok megegyezik a telepítés "egyéni".
- Egy meglévő ADSync szemben az Azure AD Connect telepítése adatbázis csak a támogatott teljes SQL. Nem támogatott az SQL Express localdb programba. Ha van meglévő ADSync adatbázis LocalDB, amelyet használni kíván, először az ADSync adatbázis (LocalDB) biztonsági mentését, és visszaállítja a teljes SQL. Amely után a visszaállított adatbázis ezzel a módszerrel az Azure AD Connect telepítése.
- A telepítéshez használt Azure AD Connect verziójának meg kell felelnie a következő feltételeknek:
    - 1.1.613.0 vagy újabb, és
    - Azonos vagy nagyobb, mint az Azure AD Connect az ADSync adatbázis legutóbbi használt verzió. Ha az Azure AD Connect telepítéshez használt verziószáma magasabb, mint az ADSync adatbázis a legutóbbi használt verzió, majd a teljes szinkronizálás lehet szükség.  Teljes szinkronizálás szükség, ha a két különböző példányainak verzióiban séma vagy a szinkronizálási szabály módosul. 
- Az ADSync adatbázis tartalmaznia kell egy viszonylag új szinkronizálási állapotát. A meglévő ADSync adatbázis a legutóbbi szinkronizálás tevékenységet az elmúlt három hétben belül kell lennie.
- Az Azure AD Connect "meglévő adatbázis használata" metódussal telepítésekor az előző az Azure AD Connect-kiszolgálón konfigurált bejelentkezési módszer nem őrződik meg. Bejelentkezési módszer továbbá telepítése közben nem konfigurálhatók. Csak konfigurálhatja a bejelentkezési módszer, telepítés befejezése után.
- Az ADSync adatbázis megosztása több Azure AD Connect-kiszolgálók nem rendelkezhet. A "meglévő adatbázis használata" módszerrel újból egy új Azure AD Connect-kiszolgáló ADSync adatbázis. Megosztás nem támogatja.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>A "meglévő adatbázis használata" mód az Azure AD Connect telepítésének lépéseit
1.  Töltse le a Windows server az Azure AD Connect telepítőt (AzureADConnect.MSI). Kattintson duplán az Azure AD Connect telepítőt az Azure AD Connect telepítésének megkezdéséhez.
2.  Az MSI-telepítés befejezése után az Azure AD Connect varázsló kezdődik-e az Expressz mód beállítása. Bezárhatja a képernyőt a kilépési ikonjára kattint.
![üdvözlet](media/active-directory-aadconnect-existing-database/db1.png)
3.  Indítsa el egy új parancssor vagy PowerShell-munkamenetben. Navigáljon a mappa <drive>\program files\Microsoft az Azure AD Connect. Futtassa a parancsot.\AzureADConnect.exe /useexistingdatabase az Azure AD Connect varázsló elindításához a "Meglévő adatbázis használata" telepítési módját.
![PowerShell](media/active-directory-aadconnect-existing-database/db2.png)
4.  Üdvözli az Azure AD Connect képernyő feladatindítót. Ha elfogadja a licencfeltételeket és az adatvédelmi nyilatkozatot, kattintson **Folytatás**.
![üdvözlet](media/active-directory-aadconnect-existing-database/db3.png)
5.  Az a **szükséges összetevők telepítése** képernyő, a **meglévő SQL Server használata** engedélyezve van. Adja meg az SQL futtató kiszolgáló, az ADSync adatbázis nevét. Ha az ADSync adatbázis üzemeltetéséhez használt SQL-adatbázismotor példány nem az alapértelmezett példány az SQL Server, az SQL-adatbázismotor példány nevét kell megadnia. Továbbá ha SQL tallózása nem engedélyezett, is meg kell SQL motor példány port számát. Példa:         
![üdvözlet](media/active-directory-aadconnect-existing-database/db4.png)           

6.  Az a **az Azure AD Connect** képernyő, meg kell adnia az Azure AD-címtár globális rendszergazda hitelesítő adatait. Az ajánljuk, hogy olyan fiókot használjon a alapértelmezett onmicrosoft.com tartomány. Ez a fiók kizárólag egy Azure AD-szolgáltatásfiók létrehozására lesz használva, és csak a varázsló befejeztéig.
![Kapcsolódás](media/active-directory-aadconnect-existing-database/db5.png)
 
7.  Az a **csatlakoztassa a címtárakat** képernyőn, a meglévő AD-erdőben beállítani a címtár-szinkronizálás mellett a vörös kereszt ikon szerepel. Szinkronizálni a módosításokat a helyszíni AD-erdőben, az AD DS-fiókjához szükség. Az Azure AD Connect varázsló nem tudja lekérni a az ADSync adatbázis tárolása, mert a hitelesítő adatok lesznek titkosítva, és csak a korábbi Azure AD Connect-kiszolgáló visszafejthető Tartományi fiók hitelesítő adatait. Kattintson a **módosítás hitelesítő adatok** adja meg a az AD-erdőhöz tartozó Tartományi fiókot.
![Könyvtárak](media/active-directory-aadconnect-existing-database/db6.png)
 
 
8.  Az előugró párbeszédpanelen választhatja (i) adjon meg egy vállalati rendszergazda hitelesítő adatai, és lehetővé teszik az Azure AD Connect hozza létre az AD DS-fiókjához, vagy (ii) létrehozhatja a Tartományi fiókot, és adja meg a hitelesítő adatok az Azure AD Connect. Ha a beállítást, és adja meg a szükséges hitelesítő adatokat, kattintson **OK** a felugró párbeszédpanel bezárásához.
![üdvözlet](media/active-directory-aadconnect-existing-database/db7.png)
 
 
9.  A megadott hitelesítő adatok vannak, ha a vörös kereszt ikon egy zöld osztásjelek ikon helyére. Kattintson a **Tovább** gombra.
![üdvözlet](media/active-directory-aadconnect-existing-database/db8.png)
 
 
10. Az a **beállíthatja az** kattintson **telepítése**.
![üdvözlet](media/active-directory-aadconnect-existing-database/db9.png)
 
 
11. Miután a telepítés befejeződött, az Azure AD Connect-kiszolgáló automatikusan az átmeneti környezetű üzemmód engedélyezve van. Javasoljuk, hogy tekintse át a kiszolgáló konfigurációját és a függőben lévő exportálásokról váratlan változásokat az átmeneti környezetű üzemmód letiltása előtt. 

## <a name="next-steps"></a>Következő lépések

- Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](active-directory-aadconnect-whats-next.md).
- Ismerkedjen meg a következő, a telepítéssel engedélyezett szolgáltatásokkal: a [Véletlen törlések megakadályozása](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).
- Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](active-directory-aadconnectsync-feature-scheduler.md).
- További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
