---
title: "Az Azure AD Connect: Előfeltételek és hardver |} Microsoft Docs"
description: "Ez a témakör ismerteti a szükséges előfeltételek és a hardverkövetelmények az Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 9c35e796cb823b2b059b726f099d658ee5e8192b
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="prerequisites-for-azure-ad-connect"></a>Előfeltételek az Azure AD Connect
Ez a témakör ismerteti a szükséges előfeltételek és az Azure AD Connect hardverkövetelményeinek.

## <a name="before-you-install-azure-ad-connect"></a>Az Azure AD Connect telepítése előtt
Az Azure AD Connect telepítése előtt van néhány dolog, amelyekre szüksége van.

### <a name="azure-ad"></a>Azure AD
* Azure-előfizetés vagy egy [Azure próba-előfizetés](https://azure.microsoft.com/pricing/free-trial/). Ez az előfizetés csak akkor szükséges, az Azure-portál elérése nem pedig Azure AD Connect használatával. Ha a PowerShell vagy az Office 365 használ, akkor nem szüksége Azure-előfizetés használatára az Azure AD Connect. Ha az Office 365-licencre van, majd is használhatja az Office 365 portálra. A fizetős Office 365-licenccel rendelkező is letölthető be az Azure portálon az Office 365 portálra.
  * Használhatja a [Azure-portálon](https://portal.azure.com). Ezen a portálon nem követeli meg az Azure AD-licencre.
* [A tartományok hozzáadásának és hitelesítésének](../active-directory-domains-add-azure-portal.md) alkalmazással tervezi használni az Azure ad-ben. Például ha azt tervezi, hogy a contoso.com használjon a felhasználók számára, majd győződjön meg arról, hogy a tartomány ellenőrzése után, és nem csak a contoso.onmicrosoft.com alapértelmezett tartomány használata.
* Az Azure AD-bérlő lehetővé teszi az alapértelmezett 50k objektumok. Ellenőrizze a tartomány, a korlátját 300 k objektumok. Ha még több objektumot az Azure ad-ben, akkor szüksége kell rendelkeznie a korlát még tovább növelése támogatási esetet megnyitásához. Ha több mint 500 KB-os objektumokat, majd licenccel kell rendelkeznie, mint például az Office 365, Azure AD alapvető, Azure AD Premium számára, vagy a nagyvállalati mobilitási és biztonsági.

### <a name="prepare-your-on-premises-data"></a>A helyszíni adatok előkészítése
* Használjon [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) hibák például az ismétlődések és a címtár-formázási problémák azonosításához az Azure AD szinkronizálása előtt és az Office 365.
* Felülvizsgálati [engedélyezheti az Azure ad-ben nem kötelező a szinkronizálási funkciók](active-directory-aadconnectsyncservice-features.md) kiértékelheti, engedélyeznie kell a szolgáltatásokat.

### <a name="on-premises-active-directory"></a>Helyszíni Active Directory
* Az AD séma verziója és az erdő működési szintje Windows Server 2003 vagy újabb kell lennie. A tartományvezérlők is valamely kiadását futtatja, mindaddig, amíg a séma- és erdő szintű követelmények teljesülnek.
* Ha azt tervezi, a szolgáltatás használatához **jelszóvisszaírás**, akkor a tartományvezérlők kell lennie a Windows Server 2008 (legújabb szervizcsomaggal) vagy újabb. Ha a tartományvezérlők 2008 (R2 előtti), akkor is telepítenie kell [gyorsjavítás KB2386717](http://support.microsoft.com/kb/2386717).
* A használt Azure ad-tartományvezérlő írhatónak kell lennie. Az **nem támogatott** használatára egy írásvédett tartományvezérlő (csak olvasható tartományvezérlő) és az Azure AD Connect nem bármely írási átirányítások követése.
* Az **nem támogatott** használatához a helyi erdők/tartományok által (egyetlen címke tartományok) használatával.
* Az **nem támogatott** használatához a helyi erdők/tartományok "pontozott" (nevében pont szerepel ".") NetBios-nevét.
* Javasoljuk, hogy [az Active Directory Lomtár engedélyezése](active-directory-aadconnectsync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Azure AD Connect server
* Az Azure AD Connect nem telepíthető Small Business Server vagy Windows Server Essentials. A kiszolgáló Windows Server standard vagy jobb kell használnia.
* Az Azure AD Connect-kiszolgáló telepítve teljes grafikus felhasználói Felülettel kell rendelkeznie. Az **nem támogatott** telepítése server core-on.
* Az Azure AD Connect a Windows Server 2008 vagy újabb rendszerre telepíthető. Ez a kiszolgáló lehet egy tartományvezérlő vagy tagkiszolgáló gyorsbeállítások használata esetén. Ha egyéni beállításokat használja, a kiszolgáló is lehet önálló, és nem kell tartományhoz csatlakoztatni.
* Az Azure AD Connect telepítése a Windows Server 2008 vagy Windows Server 2008 R2, majd győződjön meg arról, hogy a legújabb gyorsjavítások alkalmazása a Windows Update. A telepítés nincs már nem javított kiszolgálóról indul el.
* Ha azt tervezi, a szolgáltatás használatához **jelszó-szinkronizálás**, akkor az Azure AD Connect-kiszolgáló kell lennie a Windows Server 2008 R2 SP1 vagy újabb.
* Ha szeretné használni a **csoport felügyelt szolgáltatásfiók**, akkor az Azure AD Connect-kiszolgáló kell lennie a Windows Server 2012 vagy újabb.
* Rendelkeznie kell az Azure AD Connect-kiszolgáló [.NET-keretrendszer 4.5.1](#component-prerequisites) vagy újabb és [Microsoft PowerShell 3.0](#component-prerequisites) vagy újabb verziója.
* Az Azure AD Connect-kiszolgáló nem lehet írjanak elő csoportházirend PowerShell engedélyezve van.
* Active Directory összevonási szolgáltatások telepítése történik, ha a kiszolgálók, ahol az AD FS és a webalkalmazás-Proxy telepítése történik kell lennie a Windows Server 2012 R2 vagy újabb. [Rendszerfelügyeleti webszolgáltatások](#windows-remote-management) ezek a kiszolgálók távoli telepítéséhez engedélyezve kell lennie.
* Ha az Active Directory összevonási szolgáltatások telepítése történik, akkor [SSL-tanúsítványok](#ssl-certificate-requirements).
* Ha az Active Directory összevonási szolgáltatások telepítése, majd konfigurálja a [névfeloldás](#name-resolution-for-federation-servers).
* Ha a globális rendszergazdák többtényezős hitelesítés engedélyezve van, majd az URL-cím **https://secure.aadcdn.microsoftonline-p.com** szerepelnie kell a megbízható helyek listájához. A hely hozzáadása a megbízható helyek listájához, ha kéri az MFA-kérdést, és nem hozzáadta előtt kéri. Az Internet Explorer segítségével vegye fel a megbízható helyekhez.

### <a name="sql-server-used-by-azure-ad-connect"></a>Az Azure AD Connect által használt SQL Server
* Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Alapértelmezés szerint telepítve van egy SQL Server 2012 Express LocalDB (az SQL Server Express egy világos verzió). SQL Server Express méretkorlátja 10 GB-os, amely lehetővé teszi a körülbelül 100 000 objektumok kezelése. Ha nagyobb mennyiségű directory-objektumok kezelése van szüksége, a telepítővarázsló mutasson az SQL Server egy másik telepítésre szeretné.
* Egy külön SQL Server használatakor, majd ezek a követelmények vonatkoznak:
  * Az Azure AD Connect támogatja az összes változatban is elkészíti a Microsoft SQL Server SQL Server 2008 (a legújabb szervizcsomaggal) az SQL Server 2016 SP1. A Microsoft Azure SQL-adatbázis **nem támogatott** -adatbázisként.
  * Nem betűérzékeny SQL-rendezést kell használnia. Ezek rendezések azonosítják a \_CI_ neve. Az **nem támogatott** egy kis-és nagybetűket rendezés használatára, által azonosított \_CS_ neve.
  * SQL-példányonként egy szinkronizálási motor csak akkor is. Az **nem támogatott** FIM vagy MIM Sync, a DirSync vagy az Azure AD Sync SQL-példány megosztása.

### <a name="accounts"></a>Fiókok
* Egy Azure AD globális rendszergazdai fiókot az Azure AD-bérlő kívánja integrálni. Ennek a fióknak kell lennie egy **iskolai vagy a szervezeti fiók** , és nem lehet egy **Microsoft-fiók**.
* Ha a gyorsbeállítások használata, vagy a frissítésre a Dirsyncről, majd rendelkeznie kell egy vállalati rendszergazdai fiók a helyi Active Directory.
* [Az Active Directory fiókok](active-directory-aadconnect-accounts-permissions.md) használatakor az egyéni beállítások telepítési útvonalat.

### <a name="connectivity"></a>Kapcsolatok
* Az Azure AD Connect-kiszolgáló intranetes és internetes DNS-feloldás szüksége van. A DNS-kiszolgáló nevét, mind a helyszíni Active Directory és az Azure AD-végpontok feloldásához képesnek kell lennie.
* Ha az intraneten lévő tűzfalak vannak, és nyissa meg a portok az Azure AD Connect-kiszolgálók és a tartományvezérlők között, majd tekintse meg kell [az Azure AD Connect portok](active-directory-aadconnect-ports.md) további információt.
* Ha a proxy és tűzfal korlátozza, mely URL-címeket is elérhetők, akkor az URL-címek részletes ismertetését lásd: [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) kell megnyitni.
  * Ha a Microsoft Cloud Németországban vagy a Microsoft Azure Government felhő használ, majd tekintse meg [az Azure AD Connect szinkronizálási szolgáltatás példányának szempontok](active-directory-aadconnect-instances.md) URL-címek esetén.
* Az Azure AD Connect (1.1.614.0 verziót, és utána) alapértelmezés szerint használja a TLS 1.2 a szinkronizálási motor és az Azure AD közötti kommunikáció titkosítására. Ha nem érhető el az alapul szolgáló operációs rendszert a TLS 1.2-es Azure AD Connect Növekményesen visszaáll régebbi protokollok (a TLS 1.1 és TLS 1.0). Például Windows Server 2008 rendszeren futó Azure AD Connect használja a TLS 1.0, mivel a Windows Server 2008 nem támogatja a TLS 1.1 és TLS 1.2-es.
* Előtt verzió 1.1.614.0 alapértelmezés szerint az Azure AD Connect a szinkronizálási motor és az Azure AD közötti kommunikáció titkosítására használja a TLS 1.0. A TLS 1.2, kövesse a lépéseket [TLS 1.2 engedélyezése az Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Ha egy kimenő proxy alkalmaz csatlakozik az internetre, a következő beállítást a **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** fájlt fel kell venni a telepítési varázsló és az Azure AD Csatlakozni az internethez, és az Azure AD sync csatlakoztassa. Ez a szöveg meg kell adni a fájl alján. Ebben a kódban &lt;PROXYADRESS&gt; a tényleges proxy IP-címét vagy állomásnevét nevét jelöli.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Ha a proxykiszolgáló hitelesítést igényel, majd a [szolgáltatásfiók](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) a tartományban kell lennie, és adja meg a testreszabott beállítások telepítési útvonalat kell használnia egy [egyéni szolgáltatásfiók](active-directory-aadconnect-get-started-custom.md#install-required-components). Is kell a machine.config különböző módosítva lett. Ez a változás a machine.config rendelkező a telepítési varázsló és a szinkronizálási motor hitelesítési kérésekre válaszol a proxykiszolgáló. Az összes telepítési varázslólapok, kivéve a **konfigurálása** lapon, az aláírt felhasználó hitelesítő adatait használja. Az a **konfigurálása** lapján a telepítési varázsló, a környezet végén át lett váltva a [szolgáltatásfiók](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) Ön által létrehozott. A machine.config szakasz kell kinéznie.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Az Azure AD Connect egy webes kérést küld az Azure AD-címtár-szinkronizálás részeként, ha az Azure AD válaszolni legfeljebb 5 percet is igénybe vehet. Esetében a kapcsolat üresjárati időtúllépés konfigurációval rendelkezik a proxykiszolgálókra vonatkozó gyakori. Ellenőrizze, hogy a konfiguráció legalább 6 perc vagy több beállítása.

További információkért tekintse meg az MSDN a [proxy elem alapértelmezett](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
További információk, problémák adódnak a kapcsolódással, ha: [kapcsolódási problémák megoldásáról](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Egyéb
* Választható lehetőség: Egy tesztfelhasználói fiókja szinkronizálási ellenőrzése.

## <a name="component-prerequisites"></a>Összetevő-Előfeltételek
### <a name="powershell-and-net-framework"></a>PowerShell és a .net keretrendszer
Az Azure AD Connect Microsoft PowerShell és a .NET-keretrendszer 4.5.1 függ. Ebben a verzióban vagy a kiszolgálóra telepített egy újabb verziója szükséges. A Windows Server verziójától függően tegye a következőket:

* Windows Server 2012R2
  * Alapértelmezés szerint telepítve van a Microsoft PowerShell. Nincs szükség semmilyen műveletre.
  * .NET-keretrendszer 4.5.1 és a későbbi kibocsátásokban megtörténik a Windows Update szolgáltatáson keresztül érhető el. Győződjön meg arról, hogy telepítette a legújabb frissítéseket a Windows Server, a Vezérlőpult.
* Windows Server 2008R2 és a Windows Server 2012-ben
  * A Microsoft PowerShell legújabb verziója érhető el a **Windows Management Framework 4.0**, elérhető [Microsoft Download Center](http://www.microsoft.com/downloads).
  * .NET-keretrendszer 4.5.1 és a későbbi kibocsátásokban megtörténik a használhatók [Microsoft Download Center](http://www.microsoft.com/downloads).
* Windows Server 2008
  * A PowerShell legújabb támogatott verziója érhető el a **Windows Management Framework 3.0**, elérhető [Microsoft Download Center](http://www.microsoft.com/downloads).
  * .NET-keretrendszer 4.5.1 és a későbbi kibocsátásokban megtörténik a használhatók [Microsoft Download Center](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Az Azure AD Connect engedélyezni a TLS 1.2-es
Előtt verzió 1.1.614.0 alapértelmezés szerint az Azure AD Connect a Szinkronizáló vezérlő kiszolgálója és az Azure AD közötti kommunikáció titkosítására használja a TLS 1.0. .Net-alkalmazások a TLS 1.2 alapértelmezés szerint a kiszolgálón az konfigurálásával módosíthatja. További információ a TLS 1.2 megtalálhatók [Microsoft biztonsági tanácsadó 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. A TLS 1.2 Windows Server 2008 nem engedélyezhető. Windows Server 2008R2 van szüksége, vagy később. Győződjön meg arról, hogy a .net 4.5.1 gyorsjavítás az operációs rendszer telepítése című [Microsoft biztonsági tanácsadó 2960358](https://technet.microsoft.com/security/advisory/2960358). Lehetséges, hogy ez a gyorsjavítás vagy a kiszolgálóra már telepített egy újabb verzióját.
2. Windows Server 2008R2 használja, majd győződjön meg arról, a TLS 1.2 engedélyezve van. A Windows Server 2012 rendszerű kiszolgáló és újabb verzióiban a TLS 1.2 már engedélyezni kell.
   ```
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   ```
3. Minden operációs rendszerek esetén állítsa be ezt a beállításkulcsot, és indítsa újra a kiszolgálót.
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
   "SchUseStrongCrypto"=dword:00000001
   ```
4. Ha is engedélyezni szeretné a TLS 1.2 a Szinkronizáló vezérlő kiszolgálója és egy távoli SQL Server között, akkor győződjön meg arról, hogy rendelkezik a szükséges verziók telepítve [TLS 1.2-es Microsoft SQL Server támogatása](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Előfeltételek összevonási telepítése és konfigurálása
### <a name="windows-remote-management"></a>Rendszerfelügyeleti webszolgáltatások
Az Azure AD Connect telepítése az Active Directory összevonási szolgáltatások vagy a webalkalmazás-Proxy használatakor ellenőrizze ezeket a követelményeket:

* Ha a célkiszolgáló tartományhoz csatlakozik, majd győződjön meg arról, hogy engedélyezve van-e a Windows távoli felügyelete
  * Egy emelt szintű PSH parancs ablakban paranccsal`Enable-PSRemoting –force`
* Ha a célkiszolgáló egy a tartományhoz nem csatlakoztatott WAP gépen, akkor néhány további követelmények
  * A cél számítógépen (WAP gép):
    * Győződjön meg arról a Rendszerfelügyeleti webszolgáltatások (Rendszerfelügyeleti webszolgáltatások / WS-Management) szolgáltatás fut-e a szolgáltatások beépülő modul használatával
    * Egy emelt szintű PSH parancs ablakban paranccsal`Enable-PSRemoting –force`
  * A számítógépen, amelyen a varázsló fut (Ha a célszámítógép a tartományhoz nem csatlakozó vagy nem megbízható tartomány):
    * Egy emelt szintű PSH parancs ablakban a parancs használata`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * A Kiszolgálókezelőben:
      * gépkészlet DMZ WAP állomás hozzáadása (a Kiszolgálókezelő -> kezelés -> kiszolgálók hozzáadása... DNS lapon)
      * A Kiszolgálókezelő minden kiszolgálók lap: kattintson jobb gombbal a WAP-kiszolgáló, és válassza ki a kezelés másként..., a WAP-számítógép helyi (nem tartományi) hitelesítő adatok megadása
      * Távoli PSH kapcsolatot, a Kiszolgálókezelő minden kiszolgáló lap ellenőrzése: kattintson jobb gombbal a WAP-kiszolgáló, és válassza ki a Windows PowerShell. A távoli PSH munkamenet meg kell nyitnia a hozhatók létre a távoli PowerShell-munkamenetekben biztosításához.

### <a name="ssl-certificate-requirements"></a>SSL-tanúsítványokkal szemben támasztott követelmények
* Határozottan javasoljuk egy SSL tanúsítvány használata a az AD FS farm összes csomópontja és az összes webalkalmazás-proxy kiszolgálók.
* A tanúsítványnak kell lennie egy X509 tanúsítványt.
* Egy önaláírt tanúsítványt az összevonási kiszolgálóján egy tesztkörnyezetben használhatja. Azonban éles környezetbe, azt javasoljuk, hogy a nyilvános hitelesítésszolgáltatótól beszerezte a tanúsítványt.
  * Ha, amely nincs nyilvánosan megbízható tanúsítványt használ, győződjön meg arról, hogy minden webalkalmazás-Proxy kiszolgálón telepített tanúsítvány megbízható, mind a helyi kiszolgálón, és az összes összevonási kiszolgálóján
* A tanúsítvány azonosítóját meg kell egyeznie az összevonási szolgáltatás nevét (például sts.contoso.com).
  * Az identitás vagy a tulajdonos alternatív nevével (SAN) kiterjesztését típus dNSName vagy, ha nincsenek SAN bejegyzések, a tulajdonos neve szerepel-e a köznapi nevet.  
  * Több SAN-bejegyzés megadott egyik az összevonási szolgáltatás neve megegyezik a tanúsítványban lévő lehet.
  * Ha tervezi használni a munkahelyi csatlakoztatást, egy további SAN kell-e a értékű **enterpriseregistration.** a szervezet, például az egyszerű felhasználónév (UPN) utótag követ **: enterpriseregistration.contoso.com**.
* A CryptoAPI next generation (CNG) kulcsokat és a kulcstároló-szolgáltatók alapján tanúsítványok használata nem támogatott. Ez azt jelenti, hogy a kriptográfiai Szolgáltató (kriptográfiai szolgáltató) és a nem kulcstároló-Szolgáltatóra (kulcstároló-szolgáltató) alapján tanúsítványt kell használnia.
* Helyettesítő tanúsítványokat támogatottak.

### <a name="name-resolution-for-federation-servers"></a>Összevonási kiszolgálók névfeloldása
* Állítsa be a DNS-rekordok az AD FS összevonási szolgáltatás nevét (például sts.contoso.com) az intraneten (a belső DNS-kiszolgáló), mind az extranetes (nyilvános DNS a tartományregisztráló keresztül). Az intranetes DNS-rekordot, győződjön meg arról, hogy használja-e A rekordok, és nem a CNAME-rekordot. Ez elengedhetetlen ahhoz a tartományhoz csatlakozó számítógépeken a megfelelő működéséhez a windows-hitelesítést.
* Egynél több AD FS-kiszolgálón vagy webalkalmazás-Proxy kiszolgáló telepítése, majd győződjön meg arról, hogy a terheléselosztó konfigurálta, hogy az AD FS összevonási szolgáltatás nevét (például sts.contoso.com) DNS-rekordjait a terheléselosztó mutatnak.
* Integrált windows-hitelesítés működéséhez Internet Explorerrel intranet böngészőben megjelenő alkalmazásokba győződjön meg arról, hogy az AD FS összevonási szolgáltatás nevét (például sts.contoso.com) hozzáadódik az Internet Explorer intranetes zónájába. Az ellenőrzött csoportházirend segítségével és a tartományhoz csatlakoztatott számítógépeken telepítve.

## <a name="azure-ad-connect-supporting-components"></a>Az Azure AD Connect támogató összetevők
Az Azure AD Connect telepíti a kiszolgálóra, amelyen telepítve van-e az Azure AD Connect összetevők listáját a következő: A lista létrehozási alapvető Expressz telepítés. Ha a szinkronizálási szolgáltatások telepítése lapon egy másik SQL Server használatát választja, akkor az SQL Express LocalDB nincs telepítve helyileg.

* Azure AD Connect Health
* Microsoft Online Services bejelentkezési segéd (de nem függőségi viszonyban telepítve) informatikai szakemberek számára
* Microsoft SQL Server 2012 parancssori segédeszközök
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 natív ügyfél
* A Microsoft Visual C++ 2013 Újraterjesztési csomag

## <a name="hardware-requirements-for-azure-ad-connect"></a>Az Azure AD Connect hardverkövetelményei
Az alábbi táblázat az Azure AD Connect sync számítógépén minimális követelményeinek.

| Az Active Directory-objektumok száma | CPU | Memory (Memória) | Merevlemez mérete |
| --- | --- | --- | --- |
| Kevesebb mint 10 000 |1,6 GHz-es |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz-es |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz-es |16 GB |100 GB |
| 100 000 vagy több objektumot az SQL Server teljes verzióját pedig szükséges | | | |
| 100,000–300,000 |1,6 GHz-es |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz-es |32 GB |450 GB |
| Legfeljebb 600 000 |1,6 GHz-es |32 GB |500 GB |

Az AD FS vagy webalkalmazás-kiszolgálók minimális követelményei a következő:

* CPU: Két alapvető 1,6 GHz-es vagy újabb
* MEMÓRIA: 2 GB-os vagy újabb
* Azure virtuális gép: A2 konfigurációs vagy újabb

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
