---
title: Az Azure AD Connect telepítése meglévő ADSync-adatbázis segítségével |} A Microsoft Docs
description: Ez a témakör ismerteti, hogyan használhatja a meglévő ADSync-adatbázis.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: f863c4b115616ff709634f3c68955c3be3241707
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494187"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Azure AD Connect használatával a meglévő ADSync-adatbázis telepítése
Az Azure AD Connect adatok tárolásához SQL Server-adatbázis szükséges. Az alapértelmezett Azure AD connecttel telepített SQL Server 2012 Express LocalDB használata, vagy használja a saját SQL teljes verziójával. Korábban amikor telepítette az Azure AD Connect, az egy új adatbázist ADSync mindig létrejött. Az Azure AD Connect verziója 1.1.613.0 (vagy után) lehetősége van az Azure AD Connect telepítése meglévő ADSync-adatbázis jelzésével.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Meglévő ADSync-adatbázis használatának előnyei
Meglévő ADSync-adatbázis mutat:

- Hitelesítő adatokra, kivéve a szinkronizálási konfiguráció az ADSync-adatbázis (beleértve az egyéni szinkronizálási szabályait, összekötők, szűréssel és a választható funkciók konfigurálása) tárolja automatikusan helyre, és a telepítés során használt . Szinkronizálni a módosításokat az Azure AD Connect által használt hitelesítő adatokat a helyszíni AD és az Azure AD titkosított, és a csak az előző az Azure AD Connect-kiszolgáló elérhető.
- Az azonosító adatok (összekötőterek és metaverzum társítva) és a szinkronizálási cookie-k, az ADSync-adatbázis tárolja is helyre. Az újonnan telepített Azure AD Connect kiszolgáló továbbra is, ahol az előző az Azure AD Connect-kiszolgáló abbahagyta, nem a teljes szinkronizálás végrehajtásához szükség szinkronizálás.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Olyan forgatókönyvekben, ahol meglévő ADSync-adatbázis használata előnyös
Ezeket az előnyöket hasznosak lehetnek a következő esetekben:


- Rendelkezik egy meglévő Azure AD Connect. A meglévő Azure AD Connect-kiszolgáló nem működik tovább, de az ADSync adatbázist tartalmazó SQL-kiszolgáló továbbra is működik. Új Azure AD Connect-kiszolgálót telepít, és irányítsa a meglévő ADSync-adatbázis. 
- Rendelkezik egy meglévő Azure AD Connect. Az ADSync adatbázist tartalmazó SQL server már nem működik. Lehetősége van azonban egy nemrég készítsen biztonsági másolatot az adatbázisról. Új SQL-kiszolgáló először visszaállíthatja az ADSync-adatbázis. Utána akkor egy új Azure AD Connect kiszolgáló telepítése, majd mutasson a visszaállított ADSync-adatbázis.
- Rendelkezik egy meglévő Azure AD Connect üzemelő LocalDB által használt. A 10 GB-os által megszabott felső határértéket LocalDB, mert szeretné áttelepíteni a teljes funkciókészletű SQL. Adatbázis biztonsági mentése az ADSync szolgáltatást a LocalDB adatbázisból, és állítsa vissza egy SQL-kiszolgálóhoz. Ami után újra kell telepítenie egy új Azure AD Connect-kiszolgáló, és irányítsa a visszaállított ADSync-adatbázis.
- Próbálja meg beállítani egy átmeneti kiszolgálón, és azt szeretné, hogy annak konfigurációja megegyezik az aktuális aktív kiszolgáló. Az ADSync-adatbázis biztonsági mentése, és annak visszaállítására egy másik SQL Serverre. Ami után újra kell telepítenie egy új Azure AD Connect-kiszolgáló, és irányítsa a visszaállított ADSync-adatbázis.

## <a name="prerequisite-information"></a>Előfeltételként szolgáló információk

Fontos megjegyzések figyelembe vegye figyelembe a folytatás előtt:

- Ellenőrizze, hogy tekintse át az előfeltételek telepítése az Azure AD Connect: hardver és előfeltételek, és a fiók és az Azure AD Connect telepítése szükséges engedélyekkel. A "meglévő adatbázis használata" mód használata az Azure AD Connect telepítése szükséges engedélyek megegyezik a "egyéni" telepítését.
- Egy meglévő ADSync ellen az Azure AD Connect telepítése adatbázis csak támogatott teljes SQL-lel. Nem támogatott az SQL Express LocalDB. Ha rendelkezik egy meglévő ADSync-adatbázis, amely a használni kívánt LocalDB, először az ADSync-adatbázis (LocalDB) biztonsági mentése, és állítsa vissza a teljes funkciókészletű SQL. Amely után az Azure AD Connect a visszaállított adatbázis ezzel a módszerrel telepítheti.
- Az Azure AD Connect telepítés használt verziójának meg kell felelniük a következő feltételeknek:
    - 1.1.613.0 vagy újabb, és
    - Azonos vagy magasabb, mint az Azure AD Connect az ADSync-adatbázis a legutóbb használt verzióját. Ha a telepítéshez használt Azure AD Connect verziója magasabb, mint a legutóbb használt az ADSync-adatbázis-verziót, majd a teljes szinkronizálás lehet szükség.  Teljes szinkronizálás nem szükséges, ha a két verziója közötti séma vagy a szinkronizálási szabály módosítása. 
- A használt ADSync-adatbázis tartalmaznia kell egy viszonylag új szinkronizálási állapota. A meglévő ADSync-adatbázis a legutóbbi szinkronizálás tevékenységet az elmúlt három hétben belül kell lennie.
- "Létező adatbázis használata" metódus használata az Azure AD Connect telepítése esetén nem őrződik meg bejelentkezési módszer a korábbi Azure AD Connect-kiszolgálón konfigurált. Bejelentkezési módszer, a telepítés során nem konfigurálható. Bejelentkezési módszer csak a telepítés befejezését követően konfigurálható.
- Az ADSync adatbázis megosztása több Azure AD Connect-kiszolgálók nem rendelkeznek. A "meglévő adatbázis használata" módszer lehetővé teszi, hogy újra felhasználhatja a meglévő ADSync-adatbázis egy új Azure AD Connect-kiszolgálóval. Megosztás nem támogatja.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>A "meglévő adatbázis használata" mód az Azure AD Connect telepítésének lépései
1.  Töltse le a Windows server az Azure AD Connect telepítőt (AzureADConnect.MSI). Kattintson duplán az Azure AD Connect telepítőt az Azure AD Connect telepítésének megkezdéséhez.
2.  Miután az MSI-telepítés befejeződött, az Azure AD Connect varázslója elindítja az expressz módú telepítést. A Kilépés ikonra kattintva zárja be a képernyőt.
![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db1.png)
3.  Indítson új parancssort vagy PowerShell-munkamenetet. Lépjen a <drive>\program files\Microsoft Azure AD Connect mappához. Az Azure AD Connect-varázsló meglévő adatbázist használó módban való elindításához futtassa az .\AzureADConnect.exe /useexistingdatabase parancsot.
![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
4.  Megjelenik az Azure AD Connect üdvözlőképernyője. A licencfeltételek és az adatvédelmi nyilatkozat elfogadása után kattintson a **Folytatás** gombra.
![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db3.png)
5.  **A szükséges összetevők telepítése** képernyőn a **Meglévő SQL Server használata** lehetőség engedélyezve van. Adja meg az ADSync-adatbázist futtató SQL Server nevét. Ha az ADSync-adatbázist futtató SQL-motorpéldány nem az alapértelmezett példány az SQL Serveren, meg kell adnia az SQL-motorpéldány nevét. Ha az SQL-böngészés nincs engedélyezve, meg kell adnia az SQL-motorpéldány portszámát is. Példa:         
![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db4.png)           

6.  A **Azure AD-hez való csatlakozásra** szolgáló képernyőn meg kell adnia az Azure AD-címtár globális rendszergazdájának hitelesítő adatait. Javasoljuk, hogy az alapértelmezett onmicrosoft.com tartományban található fiókot használjon. Ez a fiók kizárólag egy Azure AD-szolgáltatásfiók létrehozására lesz használva, és csak a varázsló befejeztéig.
![Kapcsolódás](./media/how-to-connect-install-existing-database/db5.png)
 
7.  A **címtárak csatlakozására** szolgáló képernyőn a címtár-szinkronizáláshoz konfigurált meglévő AD-erdő mellett egy piros kereszt ikon látható. Helyszíni AD-erdőből származó módosítások szinkronizálásához AD DS-fiók szükséges. Az Azure AD Connect varázslója nem tudja lekérni az ADSync-adatbázisban tárolt AD DS-fiók hitelesítő adatait, mert azok titkosítva vannak, és a titkosítás kizárólag az előző Azure AD Connect-kiszolgálóval oldható fel. Kattintson a **hitelesítő adatok módosításának** lehetőségére az AD-erdőhöz tartozó AD DS-fiók megadásához.
![Címtárak](./media/how-to-connect-install-existing-database/db6.png)
 
 
8.  Az előugró párbeszédpanelen a következő lehetőségek közül választhat: (i) megad vállalati rendszergazdai hitelesítő adatokat, és engedi, hogy az Azure AD Connect hozza létre az AD DS-fiókot, vagy (ii) létrehozza az AD DS-fiókot, és megadja annak hitelesítő adatait az Azure AD Connectnek. Miután kiválasztotta az egyik lehetőséget és megadta a szükséges hitelesítő adatokat, kattintson az **OK** gombra az előugró párbeszédpanel bezáráshoz.
![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db7.png)
 
 
9.  A hitelesítő adatok megadása után a piros kereszt ikon egy zöld pipa ikonra változik. Kattintson a **tovább**.
![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db8.png)
 
 
10. A **Ready to configure** (Konfigurálásra kész) képernyőn kattintson az **Install** (Telepítés) gombra.
![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db9.png)
 
 
11. Miután a telepítés befejeződött, az Azure AD Connect-kiszolgáló automatikusan engedélyezve lesz az átmeneti módhoz. Javasoljuk, hogy az átmeneti mód letiltása előtt ellenőrizze a kiszolgáló konfigurációját és a függőben lévő exportálásokat, nehogy nem várt módosításokkal kelljen számolnia. 

## <a name="post-installation-tasks"></a>Telepítés utáni feladatok
Az Azure AD Connect 1.2.65.0 előtti verziójával létrehozott adatbázis biztonsági másolatának visszaállításakor az átmeneti kiszolgáló automatikusan kiválasztja a bejelentkezési módszer, **ne konfiguráljon**. A Jelszókivonat-szinkronizálás és jelszóvisszaíró jelszóbeállításait lesz visszaállítva, amíg a többi olyan szabályzat érvényben a aktív szinkronizálási kiszolgáló a megfelelő bejelentkezési módszer később módosítania kell.  Hiba a lépések elvégzéséhez megakadályozhatja a felhasználók bejelentkezésének a kell ezen a kiszolgálón aktívvá válik.  

Az alábbi táblázat segítségével ellenőrizheti a további lépések szükségesek.

|Szolgáltatás|Lépések|
|-----|-----|
|Jelszókivonat-szinkronizálás| a Jelszókivonat-szinkronizálás és a jelszó-visszaírás beállításokat teljes mértékben visszaállnak az Azure AD Connect-verziók 1.2.65.0 kezdve.  Ha visszaállítása az Azure AD Connect egy régebbi verzióját használja, tekintse át a szinkronizálási beállítások a következő szolgáltatások biztosítására, hogy azok megfelelnek a aktív szinkronizálási kiszolgáló.  Lehet, hogy további konfigurációs lépések szükségesek.|
|Összevonás az AD FS rendszerrel|Az Azure-hitelesítés továbbra is az aktív szinkronizálási kiszolgáló konfigurálása az AD FS-házirend használata.  Ha az Azure AD Connect használatával kezelheti az AD FS farmon, igény szerint módosíthatja a bejelentkezési módszer az aktív szinkronizálást példány váljon a készenléti kiszolgáló előkészítése az AD FS összevonási.   Ha eszközbeállítások az aktív szinkronizálást kiszolgálón engedélyezve vannak, állítsa be ezeket a beállításokat ezen a kiszolgálón a "Eszközbeállítások konfigurálása" feladat futtatásával.|
|Az átmenő hitelesítés és asztali egyszeri bejelentkezés|Frissítse a bejelentkezési módszert az aktív szinkronizálási kiszolgálón a konfigurációnak megfelelően.  Ha ez nem követi az elsődleges, átmenő hitelesítést, zökkenőmentes egyszeri bejelentkezést és a kiszolgáló előléptetése le lesz tiltva, és a bérlő előfordulhat, hogy tagadják, ha nem rendelkezik előtt, a Jelszókivonat-szinkronizálás, a biztonsági mentési beállítás jelentkezzen be. Azt is vegye figyelembe, hogy átmeneti módban átmenő hitelesítés engedélyezésekor egy új hitelesítési agent telepítve lesz, regisztrálja, és fog futni, és a egy magas rendelkezésre állású ügynök, amely bejelentkezési kéréseket fogad.|
|Összevonás a PingFederate-tel|Az Azure-hitelesítés továbbra is használja a PingFederate-házirendet, az aktív szinkronizálási kiszolgáló konfigurálva.  A bejelentkezési módszer a PingFederate előkészítésekor a készenléti kiszolgáló váljon az aktív szinkronizálást példány igény szerint módosítható.  Ebben a lépésben elhalasztható, amíg nem kell vonni a pingfederate-tel további tartományokat.|
## <a name="next-steps"></a>További lépések

- Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).
- További információkért ezekről a szolgáltatásokról, amelyek a telepítéssel engedélyezett szolgáltatásokkal: [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) és [az Azure AD Connect Health](how-to-connect-health-sync.md).
- Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](how-to-connect-sync-feature-scheduler.md).
- További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
