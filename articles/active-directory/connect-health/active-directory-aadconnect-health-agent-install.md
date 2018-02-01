---
title: "Az Azure AD Connect Health-ügynök telepítése | Microsoft Docs"
description: "Ez az Azure AD Connect Health lap, amely bemutatja az AD FS- és a Sync-ügynök telepítését."
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 85a12cbfdad4a1b8fbc7c3e3ea15b91c5267d7c8
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="azure-ad-connect-health-agent-installation"></a>Az Azure AD Connect Health-ügynök telepítése
Ez a dokumentum végigvezeti az Azure AD Connect Health-ügynökök telepítésének és konfigurálásának folyamatán. Az ügynököt [innen](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent) töltheti le.

## <a name="requirements"></a>Követelmények
Az alábbi táblázat az Azure AD Connect Health használatának követelményeit sorolja fel.

| Követelmény | Leírás |
| --- | --- |
| Azure AD Premium |Az Azure AD Connect Health egy Azure AD Premium szolgáltatás, amelyhez Azure AD Premium szükséges. </br></br>További információkért lásd: [Ismerkedés az Azure AD Premium szolgáltatással](../active-directory-get-started-premium.md) </br>Egy 30 napos ingyenes próbaverzió indításához lásd: [Próbaverzió indítása.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Az Azure AD Connect Health szolgáltatás indításához az Azure AD szolgáltatásban globális rendszergazdának kell lennie |Alapértelmezés szerint kizárólag a globális rendszergazdák telepíthetik és konfigurálhatják az állapotügynököket, hogy azok elinduljanak, a portálhoz hozzáférjenek, és műveleteket hajtsanak végre az Azure AD Connect Health szolgáltatásban. További információkért lásd: [Az Azure AD-címtár felügyelete](../active-directory-administer.md). <br><br> A szerepköralapú hozzáférés-vezérlés használatával hozzáférést engedhet az Azure AD Connect Health szolgáltatáshoz más felhasználók számára is a szervezetben. További információkért lásd: [Role Based Access Control for Azure AD Connect Health](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) (Szerepköralapú hozzáférés-vezérlés az Azure AD Connect Health-hez) </br></br>**Fontos:** Az ügynökök telepítésekor használt fióknak munkahelyi vagy iskolai fióknak kell lennie. Nem lehet Microsoft-fiók. További információkért lásd: [Regisztráció az Azure-ba szervezetként](../sign-up-organization.md) |
| Az Azure AD Connect Health-ügynököt az összes célkiszolgálóra telepíteni kell | Az Azure AD Connect Health használatához Health-ügynököket kell telepítenie és konfigurálnia a célkiszolgálókon az adatok fogadásához, valamint a monitorozási és elemzési funkciók biztosításához. </br></br>Ha például az AD FS-infrastruktúrájával kapcsolatos adatokat kíván gyűjteni, az ügynököt telepíteni kell az AD FS- és a webalkalmazásproxy-kiszolgálókra. Szintén telepíteni kell az ügynököt a tartományvezérlőkre, ha a helyszíni AD DS-infrastruktúrával kapcsolatos adatokat kíván gyűjteni. </br></br> |
| Kimenő kapcsolódás az Azure szolgáltatásvégpontokra | A telepítés és a futásidő során az ügynöknek kapcsolódnia kell az Azure AD Connect Health szolgáltatás végpontjaihoz. Ha tűzfalakkal blokkolta a kimenő kapcsolatot, győződjön meg róla, hogy az alábbi végpontok fel vannak véve az engedélyezett listára: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.servicebus.windows.net – port: 5671 </li><li>&#42;.adhybridhealth.azure.com/</li><li>https://management.azure.com </li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com </li><li>https://www.office.com *ezt a végpontot a rendszer csak észlelésre használja a regisztráció során.</li><br> Az **Azure Germany** környezet esetén adjon alternatív végpontokat az engedélyezett listához:</br><li>&#42;.blob.core.cloudapi.de </li><li>&#42;.queue.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li><li>&#42;.table.core.cloudapi.de </li><li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https://management.microsoftazure.de </li><li>https://policykeyservice.aadcdi.microsoftazure.de </li><li>https://login.microsoftonline.de </li><li>https://secure.aadcdn.microsoftonline-p.de </li><li>https://www.office.com *ezt a végpontot a rendszer csak észlelésre használja a regisztráció során.</li> | 
|IP-címeken alapuló kimenő kapcsolatok | További információ az IP-cím-alapú tűzfalas szűrésről: [Azure-beli IP-tartományok](https://www.microsoft.com/en-us/download/details.aspx?id=41653).|
| A kimenő forgalom SSL-vizsgálata le van tiltva, illetve a rendszer szűri | Az ügynök regisztrációja vagy adatfeltöltési műveletei meghiúsulhatnak, ha a hálózati rétegen SSL-ellenőrzés vagy megszakítás van érvényben a kimenő forgalomra. |
| Az ügynököt futtató kiszolgáló tűzfalportjai. |Az ügynök a következőt tűzfalportok megnyitását igényli, hogy kommunikálhasson az Azure AD Health szolgáltatásvégpontjaival.</br></br><li>443-as TCP-port</li><li>5671-es TCP-port</li> |
| Az alábbi webhelyek engedélyezése, amennyiben az Internet Explorer - Fokozott biztonsági beállítások be van kapcsolva |Amennyiben az Internet Explorer – Fokozott biztonsági beállítások be van kapcsolva, az alábbi webhelyeket engedélyezni kell azon a kiszolgálón, amelyiken az ügynök telepítve lesz.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>A szervezet Azure Active Directory által megbízhatóként megjelölt összevonási kiszolgálója. Például: https://sts.contoso.com</li> |
| Gondoskodjon arról, hogy a gépen a PowerShell 4.0-s vagy újabb verziója legyen telepítve | <li>A Windows Server 2008 R2 a PowerShell 2.0-t tartalmazza, amely nem megfelelő az ügynök számára.  Információk a PowerShell frissítéséről: [Ügynökök telepítése Windows Server 2008 R2 kiszolgálókon](#agent-installation-on-windows-server-2008-r2-servers).</li><li>A Windows Server 2012 a PowerShell 3.0-t tartalmazza, amely nem megfelelő az ügynök számára.  [Frissítse](http://www.microsoft.com/en-us/download/details.aspx?id=40855) a Windows Management Framework keretrendszert.</li><li>A Windows Server 2012 R2 és az annál újabb változatok már a PowerShell megfelelően új verzióját tartalmazzák.</li>|
|A FIPS letiltása|Az Azure AD Connect Health-ügynökök nem támogatják a FIPS-t.|

## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Az Azure AD Connect Health-ügynök letöltése és telepítése
* Győződjön meg róla, hogy [teljesülnek az Azure AD Connect Health követelményei](active-directory-aadconnect-health-agent-install.md#requirements).
* Ismerkedés az Azure AD Connect Health for AD FS használatával
    * [Töltse le az Azure AD Connect Health-ügynököt az AD FS szolgáltatáshoz.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Tekintse meg a telepítési utasításokat](#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Ismerkedés az Azure AD Connect Health szinkronizálási szolgáltatás használatával
    * [Töltse le, és telepítse az Azure AD Connect legújabb verzióját.](http://go.microsoft.com/fwlink/?linkid=615771) A szinkronizálási állapotügynök az (1.0.9125.0-s verziójú vagy újabb) Azure AD Connect részeként telepíthető.
* Ismerkedés az Azure AD Connect Health for AD DS használatával
    * [Töltse le az Azure AD Connect Health-ügynököt az AD DS szolgáltatáshoz](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Tekintse meg a telepítési utasításokat](#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Az Azure AD Connect Health-ügynök telepítése az AD FS szolgáltatáshoz
Az ügynök telepítésének indításához kattintson duplán a letöltött .exe-fájlra. Az első képernyőn kattintson az Install (Telepítés) elemre.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install1.png)

Amint a telepítés befejeződött, kattintson a Configure Now (Konfigurálás most) gombra.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install2.png)

Ez elindít egy PowerShell-ablakot, amely az ügynök regisztrációs folyamatának elindítására szolgál. Ha a rendszer kéri, jelentkezzen be egy Azure AD-fiókkal, amely jogosultsággal rendelkezik az ügynökök regisztrációjához. Alapértelmezés szerint a globális rendszergazdai fiók jogosult erre.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install3.png)

A bejelentkezést követően a PowerShell továbblép. Amint befejeződött, bezárhatja a PowerShellt, és a konfiguráció véget ér.

Ezen a ponton az ügynökszolgáltatások automatikusan elindulnak, így az ügynök biztonságosan feltöltheti az adatokat a felhőszolgáltatásba.

Amennyiben nem teljesítette az előző szakaszokban vázolt összes előfeltételt, figyelmeztetések jelennek meg a PowerShell-ablakban. Az ügynök telepítése előtt mindenképp teljesítenie kell a [követelményeket](active-directory-aadconnect-health-agent-install.md#requirements). A következő képernyőfelvétel egy példa az ilyen hibákra.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install4.png)

Az ügynök telepítésének ellenőrzéséhez keresse meg a következő szolgáltatásokat a kiszolgálón. Ha a konfigurációt elvégezte, a szolgáltatásoknak már futniuk kell. Ellenkező esetben le lesznek állítva mindaddig, amíg a konfigurálás be nem fejeződött.

* Azure AD Connect Health AD FS Diagnostics szolgáltatás
* Azure AD Connect Health AD FS Insights szolgáltatás
* Azure AD Connect Health AD FS Monitoring szolgáltatás

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health-requirements/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Ügynökök telepítése Windows Server 2008 R2 kiszolgálókon
Windows Server 2008 R2 kiszolgálók esetén végezze el a következő lépéseket:

1. Győződjön meg arról, hogy a kiszolgálón az 1. vagy annál magasabb szervizcsomag fut.
2. Az ügynök telepítéséhez kapcsolja ki az Internet Explorer - Fokozott biztonsági beállításokat:
3. Telepítse a Windows PowerShell 4.0 szolgáltatást mindegyik kiszolgálón az AD Health-ügynök telepítése előtt. A Windows PowerShell 4.0 telepítése:
   * Telepítse a [Microsoft .NET keretrendszer 4.5-ös verzióját](https://www.microsoft.com/download/details.aspx?id=40779) az alábbi hivatkozás segítségével, ahonnan letöltheti az offline telepítőt.
   * Telepítse a PowerShell ISE-t (a Windows-szolgáltatásokból)
   * Telepítse a [Windows Management Framework 4.0 keretrendszert.](https://www.microsoft.com/download/details.aspx?id=40855)
   * Telepítse az Internet Explorer 10-es vagy újabb verzióját a kiszolgálón. (Ez szükséges ahhoz, hogy a Health Service el tudja végezni a hitelesítést az Ön Azure-rendszergazdai hitelesítő adataival.)
4. A Windows PowerShell 4.0 a Windows Server 2008 R2 rendszeren való telepítésével kapcsolatos további információkért lásd a wikicikket [itt](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>AD FS-naplózás engedélyezése
> [!NOTE]
> Ez a szakasz csak az AD FS-kiszolgálókra vonatkozik. Ezeket a lépéseket a webalkalmazásproxy-kiszolgálók esetében nem kell követnie.
>

Annak érdekében, hogy a használatelemzés szolgáltatás adatokat gyűjthessen és elemezhessen, az Azure AD Connect Health-ügynöknek szüksége van az AD FS-naplókra. Ezek a naplók alapértelmezés szerint nincsenek bekapcsolva. Az AD FS-naplózás engedélyezéséhez és az AD FS-naplók helyének meghatározásához az AD FS-kiszolgálókon, kövesse az alábbi lépéseket.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>Az AD FS naplózásának engedélyezése Windows Server 2008 R2 rendszeren
1. Kattintson a **Start** gombra, mutasson a **Programok**, majd a **Felügyeleti eszközök** pontra, végül kattintson a **Helyi biztonsági házirend** parancsra.
2. Lépjen a **Biztonsági beállítások\Helyi házirendek\Felhasználói jogosultságok kiosztása** mappára, majd kattintson duplán a **Biztonsági naplózás létrehozása** elemre.
3. A **Helyi biztonsági beállítások** lapon ellenőrizze, hogy az AD FS 2.0 szolgáltatásfiók szerepel-e a listában. Ha nincs a listában, a **Felhasználó vagy csoport hozzáadása** gombra kattintva adja hozzá, majd kattintson az **OK** gombra.
4. A naplózás engedélyezéséhez nyisson meg egy parancssort emelt szintű jogosultságokkal, és futtassa a következő parancsot: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Zárja be a **Helyi biztonsági házirend** lapot.
<br>   -- **Az alábbi lépések csak elsődleges AD FS-kiszolgálók esetén szükségesek.** -- </br>
6. Nyissa meg az **AD FS kezelő** beépülő modulját. Az AD FS kezelő beépülő moduljának megnyitásához kattintson a **Start** gombra, mutasson a **Programok**, majd a **Felügyeleti eszközök** pontra, végül kattintson az **AD FS 2.0 kezelő** parancsra.
7. A **Műveletek** panelen kattintson az **Összevonási szolgáltatás tulajdonságainak szerkesztése** elemre.
8. Az **Összevonási szolgáltatás tulajdonságai** párbeszédpanelen kattintson az **Események** lapra.
9. Jelölje be a **Sikernaplók** és a **Hibanaplók** jelölőnégyzeteket.
10. Kattintson az **OK** gombra.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Az AD FS naplózásának engedélyezése Windows Server 2012 R2 rendszeren
1. A **Helyi biztonsági házirend** megnyitásához kattintson a **Kiszolgálókezelő** elemre a Kezdőképernyőn, vagy a Kiszolgálókezelő elemre az asztali tálcán, majd kattintson az **Eszközök/Helyi biztonsági házirend** elemre.
2. Lépjen a **Biztonsági beállítások\Helyi házirendek\Felhasználói jogosultságok kiosztása** mappára, majd kattintson duplán a **Biztonsági naplózás létrehozása** elemre.
3. A **Helyi biztonsági beállítások** lapon ellenőrizze, hogy az AD FS szolgáltatásfiók szerepel-e a listában. Ha nincs a listában, a **Felhasználó vagy csoport hozzáadása** gombra kattintva adja hozzá, majd kattintson az **OK** gombra.
4. A naplózás engedélyezéséhez nyisson meg egy parancssort emelt szintű jogosultságokkal, és futtassa a következő parancsot: ```auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable```.
5. Zárja be a **Helyi biztonsági házirend** lapot.
<br>   -- **Az alábbi lépések csak elsődleges AD FS-kiszolgálók esetén szükségesek.** -- </br>
6. Nyissa meg az **AD FS kezelő** beépülő modulját (a Kiszolgálókezelőben kattintson az Eszközök, majd az AD FS kezelő elemre).
7. A **Műveletek** panelen kattintson az **Összevonási szolgáltatás tulajdonságainak szerkesztése** elemre.
8. Az **Összevonási szolgáltatás tulajdonságai** párbeszédpanelen kattintson az **Események** lapra.
9. Jelölje be a **Sikernaplók és a Hibanaplók** jelölőnégyzeteket, majd kattintson az **OK** gombra.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Az AD FS naplózásának engedélyezése Windows Server 2016 rendszeren
1. A **Helyi biztonsági házirend** megnyitásához kattintson a **Kiszolgálókezelő** elemre a Kezdőképernyőn, vagy a Kiszolgálókezelő elemre az asztali tálcán, majd kattintson az **Eszközök/Helyi biztonsági házirend** elemre.
2. Lépjen a **Biztonsági beállítások\Helyi házirendek\Felhasználói jogosultságok kiosztása** mappára, majd kattintson duplán a **Biztonsági naplózás létrehozása** elemre.
3. A **Helyi biztonsági beállítások** lapon ellenőrizze, hogy az AD FS szolgáltatásfiók szerepel-e a listában. Ha nincs a listában, a **Felhasználó vagy csoport hozzáadása** elemre kattintva adja hozzá az AD FS-szolgáltatásfiókot, majd kattintson az **OK** gombra.
4. A naplózás engedélyezéséhez nyisson meg egy parancssort emelt szintű jogosultságokkal, és futtassa a következő parancsot: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Zárja be a **Helyi biztonsági házirend** lapot.
<br>   -- **Az alábbi lépések csak elsődleges AD FS-kiszolgálók esetén szükségesek.** -- </br>
6. Nyissa meg az **AD FS kezelő** beépülő modulját (a Kiszolgálókezelőben kattintson az Eszközök, majd az AD FS kezelő elemre).
7. A **Műveletek** panelen kattintson az **Összevonási szolgáltatás tulajdonságainak szerkesztése** elemre.
8. Az **Összevonási szolgáltatás tulajdonságai** párbeszédpanelen kattintson az **Események** lapra.
9. Jelölje be a **Sikernaplók és a Hibanaplók** jelölőnégyzeteket, majd kattintson az **OK** gombra. Ez alapértelmezés szerint ennek engedélyezett.
10. Nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsot: ```Set-AdfsProperties -AuditLevel Verbose```.

Vegye figyelembe, hogy alapértelmezés szerint az „alapszintű” naplózási szint van engedélyezve. További információ a [Windows Server 2016 AD FS naplózási fejlesztéseiről](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-fs/operations/auditing-enhancements-to-ad-fs-in-windows-server-2016)


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Az AD FS-naplók helyének meghatározása
1. Nyissa meg az **Eseménynaplót**.
2. Lépjen a Windows-naplókra, és válassza a **Biztonság** elemet.
3. A jobb oldalon kattintson az **Aktuális naplók szűrése** lehetőségre.
4. Az Eseményforrás alatt válassza az **AD FS-naplózás** elemet.

![AD FS-naplók](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [!WARNING]
> Csoportházirenddel letiltható az AD FS-naplózás. Ha az AD FS-naplózás le van tiltva, a bejelentkezési tevékenységek használatelemzései nem érhetők el. Győződjön meg róla, hogy nem rendelkezik olyan csoportházirenddel, amely letiltja az AD FS-naplózást.>
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Az Azure AD Connect Health-ügynök telepítése szinkronizáláshoz
Az Azure AD Connect Health szinkronizálási ügynöke automatikusan települ az Azure AD Connect legújabb buildjével. Az Azure AD Connect szinkronizáláshoz való használatához le kell töltenie és telepítenie kell annak legújabb verzióját. A legújabb verziót [innen](http://www.microsoft.com/download/details.aspx?id=47594) töltheti le.

Az ügynök telepítésének ellenőrzéséhez keresse meg a következő szolgáltatásokat a kiszolgálón. Ha a konfigurációt elvégezte, a szolgáltatásoknak már futniuk kell. Ellenkező esetben le lesznek állítva mindaddig, amíg a konfigurálás be nem fejeződött.

* Azure AD Connect Health Sync Insights szolgáltatás
* Azure AD Connect Health Sync Monitoring szolgáltatás

![Az Azure AD Connect Health for Sync ellenőrzése](./media/active-directory-aadconnect-health-sync/services.png)

> [!NOTE]
> Ne feledje, hogy az Azure AD Connect Health használatához az Azure AD Premium megléte szükséges. Ha nem rendelkezik az Azure AD Premiummal, nem tudja elvégezni a konfigurálást az Azure Portalon. További információkért lásd a [követelményeket tartalmazó oldalt](active-directory-aadconnect-health-agent-install.md#requirements).
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Az Azure AD Connect Health for Sync manuális regisztrációja
Ha az Azure AD Connect sikeres telepítését követően az Azure AD Connect Health for Sync-ügynök regisztrációja meghiúsul, a következő PowerShell-parancs használatával manuálisan regisztrálhatja az ügynököt.

> [!IMPORTANT]
> A PowerShell-parancs használata csak akkor szükséges, ha az ügynök regisztrálása meghiúsul az Azure AD Connect telepítése után.
>
>

A következő PowerShell-parancs CSAK akkor szükséges, ha az állapotügynök regisztrálása meghiúsul az Azure AD Connect sikeres telepítése és konfigurációja után. Az Azure AD Connect Health szolgáltatások csak az ügynök sikeres telepítése után indulnak el.

A szinkronizálási Azure AD Connect Health-ügynök manuálisan a következő PowerShell-paranccsal telepíthető:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

A parancs a következő paramétereket fogadja:

* AttributeFiltering: $true (alapértelmezett) – ha az AD Connect nem szinkronizálja az alapértelmezett attribútumkészletet, és testre lett szabva, hogy egy szűrt attribútumkészletet használjon. $false a többi esetben.
* StagingMode: $false (alapértelmezett) – ha az Azure AD Connect-kiszolgáló NEM átmeneti módban van, és $true, ha a kiszolgáló átmeneti módra lett konfigurálva.

Amikor a rendszer a hitelesítő adatait kéri, használja ugyanazt a globális rendszergazdafiókot (például admin@domain.onmicrosoft.com), amelyet az Azure AD Connect konfigurálásához használt.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Az Azure AD Connect Health-ügynök telepítése az AD DS szolgáltatáshoz
Az ügynök telepítésének indításához kattintson duplán a letöltött .exe-fájlra. Az első képernyőn kattintson az Install (Telepítés) elemre.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Amint a telepítés befejeződött, kattintson a Configure Now (Konfigurálás most) gombra.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Ekkor megnyílik egy parancssor, utána egy PowerShell parancsmag, amely végrehajtja a Register-AzureADConnectHealthADDSAgent parancsot. Ha a rendszer arra kéri, hogy jelentkezzen be az Azure-ba, lépjen tovább, és jelentkezzen be.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

A bejelentkezést követően a PowerShell továbblép. Amint befejeződött, bezárhatja a PowerShellt, és a konfiguráció véget ér.

Ezen a ponton a szolgáltatásoknak automatikusan el kell indulniuk, lehetővé téve az ügynök számára az adatok figyelését és gyűjtését. Amennyiben nem teljesítette az előző szakaszokban vázolt összes előfeltételt, figyelmeztetések jelennek meg a PowerShell-ablakban. Az ügynök telepítése előtt mindenképp teljesítenie kell a [követelményeket](active-directory-aadconnect-health-agent-install.md#requirements). A következő képernyőfelvétel egy példa az ilyen hibákra.

![Az Azure AD Connect Health for AD DS ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Az ügynök telepítésének ellenőrzéséhez keresse meg a következő szolgáltatásokat a tartományvezérlőn.

* Azure AD Connect Health AD DS Insights szolgáltatás
* Azure AD Connect Health AD DS Monitoring szolgáltatás

Ha a konfigurációt elvégezte, ezeknek a szolgáltatásoknak már futniuk kell. Ellenkező esetben le lesznek állítva mindaddig, amíg a konfigurálás be nem fejeződött.

![Az Azure AD Connect Health ellenőrzése](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)


## <a name="agent-registration-using-powershell"></a>Ügynök regisztrációja a PowerShell használatával
A megfelelő ügynökhöz tartozó setup.exe telepítése után a szerepkörtől függően a következő PowerShell-parancsokkal végezheti el az ügynök regisztrációját. Nyisson meg egy PowerShell-ablakot, és hajtsa végre a megfelelő parancsot:

```
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Ezek a parancsok elfogadják a „Credential” paramétert a regisztráció nem interaktív, illetve Server-Core gépen való végrehajtásához.
* A hitelesítő adat rögzíthető egy PowerShell-változóban, amelyet a rendszer paraméterként ad tovább.
* Bármilyen Azure AD-identitást megadhat, amely jogosultsággal rendelkezik az ügynökök regisztrációjához, és amelyen NINCS engedélyezve az MFA.
* Alapértelmezés szerint a globális rendszergazdák rendelkeznek jogosultsággal az ügynökök regisztrációjához. Más, kevesebb jogosultságokkal rendelkező identitásokkal is végrehajtathatja ezt a lépést. További tudnivalók a [szerepköralapú hozzáférés-vezérlésről](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control).

```
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Azure AD Connect Health-ügynökök konfigurálása HTTP proxyk használatára
Az Azure AD Connect Health-ügynököket konfigurálhatja úgy, hogy HTTP proxyval működjenek.

> [!NOTE]
> * A „Netsh WinHttp set ProxyServerAddress” használata nem támogatott, mivel az ügynök a System.Net használatával adja le a webes kérelmeket, és nem a Microsoft Windows HTTP szolgáltatásokkal.
> * A rendszer a konfigurált Http proxy címét használja a titkosított Https üzenetek átengedésére.
> * A hitelesített proxyk (HTTPBasic használatával) nem támogatottak.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Állapotügynök proxykonfigurációjának módosítása
Az alábbi beállítások használhatóak az Azure AD Connect Health-ügynökök konfigurálásához a HTTP proxyk használatára.

> [!NOTE]
> A proxybeállítások frissítéséhez újra kell indítania az összes Azure AD Connect Health-ügynök szolgáltatást. Futtassa az alábbi parancsot:<br>
> Restart-Service AdHealth*
>
>

#### <a name="import-existing-proxy-settings"></a>Meglévő proxybeállítások importálása
##### <a name="import-from-internet-explorer"></a>Importálás Internet Explorerből
Az Internet Explorer HTTP-proxybeállításai importálhatók az Azure AD Connect Health-ügynökök általi használatra. A Health-ügynököt futtató minden egyes kiszolgálón hajtsa végre a következő PowerShell-parancsot:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importálás WinHTTP-ből
A WinHTTP proxybeállításai importálhatók az Azure AD Connect Health-ügynökök általi használatra. A Health-ügynököt futtató minden egyes kiszolgálón hajtsa végre a következő PowerShell-parancsot:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Proxycímek manuális megadása
A következő PowerShell-parancs futtatásával manuálisan megadhat egy proxykiszolgálót a Health-ügynököt futtató minden egyes kiszolgálón:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Példa: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443*

* Az „address” lehet egy, a DNS által feloldható kiszolgálónév vagy egy IPv4-cím
* A „port” elhagyható. Ilyen esetben a 443 lesz az alapértelmezett port.

#### <a name="clear-existing-proxy-configuration"></a>Meglévő proxykonfiguráció törlése
A meglévő proxykonfigurációt a következő parancs futtatásával törölheti:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Meglévő proxybeállítások olvasása
A jelenleg konfigurált proxybeállításokat a következő parancs futtatásával olvashatja:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Az Azure AD Connect Health szolgáltatás kapcsolódásának tesztelése
Előfordulhat, hogy hibák lépnek fel, amelyek hatására az Azure AD Connect Health-ügynök elveszíti a kapcsolatot az Azure AD Connect Health szolgáltatással. Ezek hálózati, engedélyekkel kapcsolatos és egyéb különféle hibák lehetnek.

Ha a ügynök több mint két órán keresztül képtelen adatokat küldeni az Azure AD Connect Health szolgáltatásnak, a következő szövegű riasztás jelenik meg a portálon: „Az üzemállapot-figyelő szolgáltatás adatai nem naprakészek.” A következő PowerShell-parancs futtatásával ellenőrizheti, hogy az érintett Azure AD Connect Health-ügynök fel tudja-e tölteni az adatokat az Azure AD Connect Health szolgáltatásba:

    Test-AzureADConnectHealthConnectivity -Role ADFS

A szerepkör-paraméter a következő értékeket veheti:

* ADFS
* Sync
* ADDS

A parancs -ShowResults jelzőjével megtekintheti a részletes naplókat. Használja a következő példát:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

> [!NOTE]
> A kapcsolódási eszköz használatához először el kell végeznie az ügynök regisztrációját. Amennyiben nem tudja elvégezni az ügynök regisztrációját, bizonyosodjon meg róla, hogy az Azure AD Connect Health összes [követelményének](active-directory-aadconnect-health-agent-install.md#requirements) megfelel. A kapcsolódási teszt végrehajtása alapértelmezés szerint megtörténik az ügynök regisztrációja során.
>
>

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Az Azure AD Connect Health műveletei](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health – gyakori kérdések](active-directory-aadconnect-health-faq.md)
* [Az Azure AD Connect Health verzióelőzményei](active-directory-aadconnect-health-version-history.md)
