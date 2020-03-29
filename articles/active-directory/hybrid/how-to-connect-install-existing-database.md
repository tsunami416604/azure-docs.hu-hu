---
title: Az Azure AD Connect telepítése meglévő ADSync-adatbázis használatával | Microsoft dokumentumok
description: Ez a témakör egy meglévő ADSync-adatbázis használatát ismerteti.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc6993586063c9c99a287c51d799b44f921768d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245174"
---
# <a name="install-azure-ad-connect-using-an-existing-adsync-database"></a>Az Azure AD Connect telepítése meglévő ADSync-adatbázis használatával
Az Azure AD Connect adatok tárolásához SQL Server-adatbázisszükséges. Használhatja az Azure AD Connecttel telepített alapértelmezett SQL Server 2012 Express LocalDB-t, vagy használhatja az SQL saját teljes verzióját. Korábban az Azure AD Connect telepítésekor mindig létrejött egy Új ADSync nevű adatbázis. Az Azure AD Connect 1.1.613.0-s verziójával (vagy azt követően) telepítheti az Azure AD Connectet egy meglévő ADSync-adatbázisra mutatva.

## <a name="benefits-of-using-an-existing-adsync-database"></a>Meglévő ADSync-adatbázis használatának előnyei
Meglévő ADSync-adatbázisra mutatva:

- A hitelesítő adatok kivételével az ADSync-adatbázisban tárolt szinkronizálási konfiguráció (beleértve az egyéni szinkronizálási szabályokat, az összekötőket, a szűrést és a választható szolgáltatások konfigurációját) automatikusan helyreáll és használatban van a telepítés során. . Az Azure AD Connect által a helyszíni AD-vel és az Azure AD-vel való módosítások szinkronizálásához használt hitelesítő adatok titkosítva vannak, és csak az előző Azure AD Connect-kiszolgáló által érhetők el.
- Az ADSync adatbázisban tárolt összes identitásadat (összekötőterekhez és metaverzumhoz társítva) és szinkronizálási cookie-k is helyreállnak. Az újonnan telepített Azure AD Connect-kiszolgáló továbbra is szinkronizálhat onnan, ahol az előző Azure AD Connect-kiszolgáló abbahagyta, ahelyett, hogy teljes szinkronizálást kellene végrehajtania.

## <a name="scenarios-where-using-an-existing-adsync-database-is-beneficial"></a>Olyan esetek, amikor egy meglévő ADSync-adatbázis használata előnyös
Ezek az előnyök a következő esetekben hasznosak:


- Van egy meglévő Azure AD Connect-telepítés. A meglévő Azure AD Connect-kiszolgáló már nem működik, de az ADSync-adatbázist tartalmazó SQL-kiszolgáló továbbra is működik. Telepíthet egy új Azure AD Connect-kiszolgálót, és a meglévő ADSync-adatbázisra irányíthatja azt. 
- Van egy meglévő Azure AD Connect-telepítés. Az ADSync adatbázist tartalmazó SQL-kiszolgáló már nem működik. Azonban van egy közelmúltbeli biztonsági másolatot az adatbázisról. Először visszaállíthatja az ADSync adatbázist egy új SQL-kiszolgálóra. Ezt követően telepítheti az új Azure AD Connect-kiszolgálót, és a visszaállított ADSync-adatbázisra irányíthatja.
- Van egy meglévő Azure AD Connect-központi telepítés, amely a LocalDB-t használja. A LocalDB által előírt 10 GB-os korlát miatt a teljes SQL-re szeretne áttérni. Az ADSync adatbázisról biztonsági másolatot lehet tenni a LocalDB-ről, és visszaállíthatja egy SQL-kiszolgálóra. Ezt követően újratelepítheti az új Azure AD Connect-kiszolgálót, és a visszaállított ADSync-adatbázisra irányíthatja.
- Átmeneti kiszolgálót próbál beállítani, és meg akar győződni arról, hogy a konfigurációja megegyezik az aktuális aktív kiszolgáló konfigurációjával. Biztonsági másolatot lehet tenni az ADSync adatbázisról, és visszaállíthatja egy másik SQL-kiszolgálóra. Ezt követően újratelepítheti az új Azure AD Connect-kiszolgálót, és a visszaállított ADSync-adatbázisra irányíthatja.

## <a name="prerequisite-information"></a>Előfeltételekre vonatkozó információk

Fontos megjegyzések, amelyeket a folytatás előtt figyelembe kell venni:

- Győződjön meg arról, hogy tekintse át az Azure AD Connect hardveren és előfeltételekben történő telepítésének előfeltételeit, valamint az Azure AD Connect telepítéséhez szükséges fiókot és engedélyeket. Az Azure AD Connect "meglévő adatbázis használata" módban történő telepítéséhez szükséges engedélyek megegyeznek az "egyéni" telepítéssel.
- Az Azure AD Connect telepítése egy meglévő ADSync-adatbázissal csak a teljes SQL-lel támogatott. Az SQL Express LocalDB nem támogatja. Ha van egy meglévő ADSync-adatbázisa a LocalDB-ben, amelyet használni kíván, először biztonsági másolatot kell készítenie az ADSync adatbázisról (LocalDB), és vissza kell állítania azt a teljes SQL-re. Ezt követően telepítheti az Azure AD Connect et a visszaállított adatbázis ellen ezzel a módszerrel.
- A telepítéshez használt Azure AD Connect verziójának meg kell felelnie a következő feltételeknek:
    - 1.1.613.0 vagy újabb, ÉS
    - Ugyanaz vagy magasabb, mint az Azure AD Connect legutóbbi, az ADSync-adatbázishoz használt verziója. Ha a telepítéshez használt Azure AD Connect verzió nagyobb, mint az ADSync-adatbázishoz legutóbb használt verzió, akkor teljes szinkronizálásra lehet szükség.  Teljes szinkronizálásra van szükség, ha a két verzió között séma vagy szinkronizálási szabály változás történik. 
- A használt ADSync-adatbázisnak viszonylag új szinkronizálási állapotot kell tartalmaznia. A meglévő ADSync-adatbázissal végzett utolsó szinkronizálási tevékenységnek az elmúlt három héten belül kell lennie.
- Az Azure AD Connect "meglévő adatbázis használata" módszerrel történő telepítésekor az előző Azure AD Connect-kiszolgálón konfigurált bejelentkezési módszer nem őrződik meg. Továbbá a telepítés során nem konfigurálhatja a bejelentkezési módszert. A bejelentkezési módszer csak a telepítés befejezése után konfigurálható.
- Nem rendelkezhet több Azure AD Connect-kiszolgálóval, amelyek ugyanazt az ADSync-adatbázist osztják. A "meglévő adatbázis használata" módszer lehetővé teszi, hogy egy meglévő ADSync-adatbázis tanuskodjon újra egy új Azure AD Connect-kiszolgálóval. Nem támogatja a megosztást.

## <a name="steps-to-install-azure-ad-connect-with-use-existing-database-mode"></a>Az Azure AD Connect "meglévő adatbázis használata" móddal történő telepítésének lépései
1.  Töltse le az Azure AD Connect telepítőt (AzureADConnect.MSI) a Windows-kiszolgálóra. Kattintson duplán az Azure AD Connect telepítőre az Azure AD Connect telepítésének megkezdéséhez.
2.  Miután az MSI-telepítés befejeződött, az Azure AD Connect varázslója elindítja az expressz módú telepítést. A Kilépés ikonra kattintva zárja be a képernyőt.
![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db1.png)
3.  Indítson új parancssort vagy PowerShell-munkamenetet. Keresse meg a "C:\Program Files\Microsoft Azure Active Directory Connect" mappát. Az Azure AD Connect-varázsló meglévő adatbázist használó módban való elindításához futtassa az .\AzureADConnect.exe /useexistingdatabase parancsot.

> [!NOTE]
> Csak akkor használja a **/UseExistingDatabase** kapcsolót, ha az adatbázis már tartalmaz adatokat egy korábbi Azure AD Connect-telepítésből. Például amikor egy helyi adatbázisból egy teljes SQL Server-adatbázisba költözik, vagy amikor az Azure AD Connect kiszolgálót újraépítették, és visszaállította az ADSync-adatbázis SQL biztonsági másolatát az Azure AD Connect korábbi telepítéséből. Ha az adatbázis üres, azaz nem tartalmaz adatokat egy korábbi Azure AD Connect-telepítésből, hagyja ki ezt a lépést.

![PowerShell](./media/how-to-connect-install-existing-database/db2.png)
1. Megjelenik az Azure AD Connect üdvözlőképernyője. A licencfeltételek és az adatvédelmi nyilatkozat elfogadása után kattintson a **Folytatás** gombra.
   ![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db3.png)
1. **A szükséges összetevők telepítése** képernyőn a **Meglévő SQL Server használata** lehetőség engedélyezve van. Adja meg az ADSync-adatbázist futtató SQL Server nevét. Ha az ADSync-adatbázist futtató SQL-motorpéldány nem az alapértelmezett példány az SQL Serveren, meg kell adnia az SQL-motorpéldány nevét. Ha az SQL-böngészés nincs engedélyezve, meg kell adnia az SQL-motorpéldány portszámát is. Példa:         
   ![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db4.png)           

1. A **Azure AD-hez való csatlakozásra** szolgáló képernyőn meg kell adnia az Azure AD-címtár globális rendszergazdájának hitelesítő adatait. Javasoljuk, hogy az alapértelmezett onmicrosoft.com tartományban található fiókot használjon. Ez a fiók kizárólag egy Azure AD-szolgáltatásfiók létrehozására lesz használva, és csak a varázsló befejeztéig.
   ![Kapcsolódás](./media/how-to-connect-install-existing-database/db5.png)
 
1. A **címtárak csatlakozására** szolgáló képernyőn a címtár-szinkronizáláshoz konfigurált meglévő AD-erdő mellett egy piros kereszt ikon látható. Helyszíni AD-erdőből származó módosítások szinkronizálásához AD DS-fiók szükséges. Az Azure AD Connect varázslója nem tudja lekérni az ADSync-adatbázisban tárolt AD DS-fiók hitelesítő adatait, mert azok titkosítva vannak, és a titkosítás kizárólag az előző Azure AD Connect-kiszolgálóval oldható fel. Kattintson a **hitelesítő adatok módosításának** lehetőségére az AD-erdőhöz tartozó AD DS-fiók megadásához.
   ![Címtárak](./media/how-to-connect-install-existing-database/db6.png)
 
 
1. Az előugró párbeszédpanelen a következő lehetőségek közül választhat: (i) megad vállalati rendszergazdai hitelesítő adatokat, és engedi, hogy az Azure AD Connect hozza létre az AD DS-fiókot, vagy (ii) létrehozza az AD DS-fiókot, és megadja annak hitelesítő adatait az Azure AD Connectnek. Miután kiválasztotta az egyik lehetőséget és megadta a szükséges hitelesítő adatokat, kattintson az **OK** gombra az előugró párbeszédpanel bezáráshoz.
   ![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db7.png)
 
 
1. A hitelesítő adatok megadása után a piros kereszt ikon egy zöld pipa ikonra változik. Kattintson a **Tovább** gombra.
   ![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db8.png)
 
 
1. A **Konfigurálásra kész** képernyőn kattintson a **Telepítés gombra.**
   ![Üdvözlőképernyő](./media/how-to-connect-install-existing-database/db9.png)
 
 
1. Miután a telepítés befejeződött, az Azure AD Connect-kiszolgáló automatikusan engedélyezve lesz az átmeneti módhoz. Javasoljuk, hogy az átmeneti mód letiltása előtt ellenőrizze a kiszolgáló konfigurációját és a függőben lévő exportálásokat, nehogy nem várt módosításokkal kelljen számolnia. 

## <a name="post-installation-tasks"></a>Telepítés utáni feladatok
Az Azure AD Connect 1.2.65.0 előtti verziója által létrehozott adatbázis biztonsági másolatának visszaállításakor az átmeneti kiszolgáló automatikusan kiválasztja a **Ne konfiguráljon**bejelentkezési módszert. Bár a jelszókivonat-szinkronizálási és jelszó-visszaírási beállítások visszaállnak, ezt követően módosítania kell a bejelentkezési módszert, hogy megfeleljen az aktív szinkronizálási kiszolgálóra vonatkozó egyéb házirendeknek.  A lépések végrehajtásának elmulasztása megakadályozhatja, hogy a felhasználók bejelentkezzenek, ha a kiszolgáló aktívvá válik.  

Az alábbi táblázat segítségével ellenőrizze a szükséges további lépéseket.

|Szolgáltatás|Lépések|
|-----|-----|
|Jelszókivonat-szinkronizálás| A jelszókivonat-szinkronizálási és jelszó-visszaírási beállítások teljes mértékben vissza állnak az Azure AD Connect 1.2.65.0-tól kezdődő verzióihoz.  Ha az Azure AD Connect egy régebbi verziójával visszaállítás, tekintse át a szinkronizálási beállításokat ezeka funkciók, hogy megbizonyosodjon arról, hogy megfelelnek az aktív szinkronizálási kiszolgáló.  Nincs szükség további konfigurációs lépésekre.|
|Összevonás az AD FS rendszerrel|Az Azure-hitelesítések továbbra is az aktív szinkronizálási kiszolgálóhoz konfigurált AD FS-házirendet fogják használni.  Ha az Azure AD Connect használatával kezeli az AD FS-farmot, szükség esetén módosíthatja a bejelentkezési módszert AD FS-összevonásra, hogy előkészítse a készenléti kiszolgáló aktív szinkronizálási példánysá válását.   Ha az eszközbeállítások engedélyezve vannak az aktív szinkronizálási kiszolgálón, konfigurálja ezeket a beállításokat ezen a kiszolgálón az "Eszközbeállítások konfigurálása" feladat futtatásával.|
|Átmenő hitelesítés és egységes bejelentkezés az asztalon|Frissítse a bejelentkezési módszert, hogy megfeleljen az aktív szinkronizálási kiszolgáló konfigurációjának.  Ha ezt nem követi, mielőtt a kiszolgálót elsődlegessé léptetné, az átmenő hitelesítés a zökkenőmentes egyszeri bejelentkezéssel együtt le lesz tiltva, és a bérlő zárolva lesz, ha nem rendelkezik jelszókivonat-szinkronizálással a biztonsági aláírási lehetőségként. Azt is vegye figyelembe, hogy ha átmeneti módban engedélyezi az átmenő hitelesítést, egy új hitelesítési ügynök lesz telepítve, regisztrálva lesz, és magas rendelkezésre állású ügynökként fog futni, amely elfogadja a bejelentkezési kérelmeket.|
|Összevonás a PingFederate-tel|Az Azure-hitelesítések továbbra is az aktív szinkronizálási kiszolgálóhoz konfigurált PingFederate házirendet fogják használni.  A bejelentkezési módszert szükség esetén pingfederate-re módosíthatja, hogy a készenléti kiszolgáló az aktív szinkronizálási példánysá váljon.  Ez a lépés elhalasztható, amíg további tartományokat nem kell összeegyeztetnie a PingFederate segítségével.|

## <a name="next-steps"></a>További lépések

- Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).
- Ismerkedjen meg a következő, a telepítéssel engedélyezett szolgáltatásokkal: a [Véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md) és az [Azure AD Connect Health](how-to-connect-health-sync.md).
- Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](how-to-connect-sync-feature-scheduler.md).
- További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
