---
title: 'Azure AD Connect: Előfeltételek és a hardver |} A Microsoft Docs'
description: Ez a témakör ismerteti az előfeltételeket és az Azure AD Connect hardverkövetelményeit
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd3aac6a7fb0904089f135c9af7b136eda73701f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835469"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Az Azure AD Connect előfeltételei
Ez a témakör ismerteti az előfeltételeket és az Azure AD Connect hardverkövetelményeit.

## <a name="before-you-install-azure-ad-connect"></a>Az Azure AD Connect telepítése előtt
Előtt az Azure AD Connectet telepíti, akkor kell néhány dolgot.

### <a name="azure-ad"></a>Azure AD
* Egy Azure AD-bérlő. Kap egy-egy [Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/). Az Azure AD Connect kezelése a következő portálok egyikét használhatja:
  * A [az Azure portal](https://portal.azure.com).
  * A [Office-portálon](https://portal.office.com).  
* [A tartományok hozzáadásának és hitelesítésének](../active-directory-domains-add-azure-portal.md) tervezi használni az Azure ad-ben. Például ha azt tervezi, hogy a contoso.com a felhasználók számára, majd ellenőrizze, hogy a tartomány ellenőrzése után, és csak nem használja a contoso.onmicrosoft.com alapértelmezett tartomány.
* Azure AD-bérlő alapértelmezett 50 ezer objektum lehetővé teszi. Ha a tartomány ellenőrzéséhez a korlát 300 k objektumok-ra emelkedett. Ha még több objektumot az Azure ad-ben, majd meg kell nyitnia egy támogatási esetet, még tovább növelni a korlátot. Ha több mint 500 ezer objektumokat, majd licenc szükséges, például az Office 365-höz, az Azure AD alapszintű, az Azure AD Premium vagy Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>A helyszíni adatok előkészítése
* Használat [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) hibákat, például az ismétlődések és a címtár formázási problémák azonosítására, az Azure AD szinkronizálása előtt és az Office 365.
* Felülvizsgálat [engedélyezheti az Azure AD-ben nem kötelező a szinkronizálási funkciók](how-to-connect-syncservice-features.md) és értékelje, engedélyeznie kell a szolgáltatásokat.

### <a name="on-premises-active-directory"></a>Helyszíni Active Directory
* Az AD séma verziója és az erdő működési szintje Windows Server 2003 vagy újabb verzióját kell lennie. A tartományvezérlőkön futtatható bármely verziója mindaddig, amíg a séma és az erdő szintű követelmények teljesülnek-e.
* Ha azt tervezi, hogy a funkció használatához **jelszóvisszaíró**, akkor a tartományvezérlőket kell lennie a Windows Server 2008 R2 vagy újabb.
* Az Azure AD által használt tartományvezérlő írhatónak kell lennie. Ez **nem támogatott** használata egy írásvédett tartományvezérlő (csak olvasható tartományvezérlő) és az Azure AD Connect nem minden írási átirányítások követése.
* Ez **nem támogatott** használata a helyszíni erdők/tartományok használatával "pontozott" (nevében pont szerepel ".") NetBios-nevét.
* Javasoljuk, hogy [az Active Directory Lomtár engedélyezése](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Az Azure AD Connect-kiszolgáló
* Az Azure AD Connect nem telepíthető Small Business Server vagy Windows Server Essentials 2019 előtt (a Windows Server Essentials 2019 támogatott). A kiszolgáló Windows Server standard vagy jobb kell használnia.
* Az Azure AD Connect-kiszolgáló egy teljes grafikus felhasználói Felülettel telepítve kell rendelkeznie. Ez **nem támogatott** telepítése server core-on.
* Az Azure AD Connect telepíteni kell a Windows Server 2008 R2 vagy újabb. Ez a kiszolgáló tartományhoz csatlakoztatott és a tartományvezérlő vagy tagkiszolgáló kell lennie.
* Ha az Azure AD Connect telepíti a Windows Server 2008 R2 rendszeren, majd győződjön meg arról, a alkalmazni a legújabb gyorsjavítások a Windows Update webhelyről. A telepítés nem sikerül veszéllyel kiszolgálóról indítsa el.
* Ha azt tervezi, hogy a funkció használatához **jelszó-szinkronizálás**, akkor az Azure AD Connect-kiszolgáló kell lennie a Windows Server 2008 R2 SP1 vagy újabb.
* Ha azt tervezi, hogy használjon egy **csoportosan felügyelt szolgáltatásfiók**, akkor az Azure AD Connect-kiszolgáló kell lennie a Windows Server 2012 vagy újabb.
* Rendelkeznie kell az Azure AD Connect-kiszolgáló [.NET-keretrendszer 4.5.1-es](#component-prerequisites) vagy újabb és [Microsoft PowerShell 3.0](#component-prerequisites) vagy újabb verziója szükséges.
* Az Azure AD Connect-kiszolgáló nesmí mít Beszédátírási csoportházirend PowerShell engedélyezve van.
* Ha az Active Directory összevonási szolgáltatások üzembe helyezéséhez, a kiszolgálók, ahol az AD FS vagy webalkalmazás-Proxy telepítése kell lennie a Windows Server 2012 R2 vagy újabb. [Windows-távfelügyelet](#windows-remote-management) ezek a kiszolgálók távoli telepítéséhez engedélyezve kell lennie.
* Ha az Active Directory összevonási szolgáltatások üzembe helyezéséhez, szüksége [SSL-tanúsítványok](#ssl-certificate-requirements).
* Ha az Active Directory összevonási szolgáltatások lesz üzembe helyezve, akkor konfigurálnia kell [névfeloldás](#name-resolution-for-federation-servers).
* Ha a globális rendszergazdák rendelkeznek MFA engedélyezve van, majd az URL-cím **https://secure.aadcdn.microsoftonline-p.com** kell lennie a megbízható helyek listájához. A hely hozzáadása a megbízható helyek listájához, ha az MFA-hitelesítést kér, és nem vett előtt kéri. Az Internet Explorer segítségével adja hozzá a megbízható helyekhez.

### <a name="sql-server-used-by-azure-ad-connect"></a>Az Azure AD Connect által használt SQL Server
* Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Alapértelmezés szerint telepítve van egy SQL Server 2012 Express LocalDB (egy világos verziója az SQL Server Express). Az SQL Server Express rendelkezik, amelyek segítségével kezelheti a körülbelül 100 000 objektumra 10 GB-os méretkorlátot. Ha szeretne egy nagyobb adatmennyiségek directory-objektumok kezelése, át kell irányítania a telepítési varázsló egy másik telepített SQL Server.
* Ha egy külön SQL Server használja, majd ezek a követelmények vonatkoznak:
  * Az Azure AD Connect a Microsoft SQL Server 2008 R2 (a legújabb szervizcsomaggal) az SQL Server 2019 összes verzióit támogatja. A Microsoft Azure SQL Database **nem támogatott** adatbázisként.
  * A kis-és SQL-rendezést kell használnia. Ezek rendezések utak azonosítják a \_CI_ a neve. Ez **nem támogatott** egy kis-és nagybetűket, által azonosított rendezés használatára \_CS_ a neve.
  * Legfeljebb egy SQL-példány egy szinkronizálási motor. Ez **nem támogatott** FIM vagy MIM Sync, a DirSync vagy Azure AD-Szinkronizáló egy SQL-példány megosztása.

### <a name="accounts"></a>Fiókok
* Integrálni szeretné az Azure AD-bérlő Azure AD globális rendszergazda fiókkal. Ennek a fióknak kell lennie egy **iskola vagy szervezeti fiókjával** , és nem lehet egy **Microsoft-fiók**.
* Ha a gyorsbeállítások használata, vagy a frissítés a Dirsync szolgáltatásról, majd rendelkeznie kell egy vállalati rendszergazdai fiók számára a helyszíni Active Directoryban.
* [Az Active Directory fiókok](reference-connect-accounts-permissions.md) a helyszíni Active Directory használatakor az egyéni beállítások telepítési útvonala vagy vállalati rendszergazdai fiókkal.

### <a name="connectivity"></a>Kapcsolatok
* Az Azure AD Connect-kiszolgáló intranetes és internetes is szüksége van a DNS-feloldását. A DNS-kiszolgáló a névfeloldást mind a helyszíni Active Directory és az Azure AD-végpontok képesnek kell lennie.
* Ha az intraneten lévő tűzfalak vannak, és az Azure AD Connect kiszolgálók és a tartományvezérlők közötti portok megnyitásához, majd tekintse meg kell [az Azure AD Connect portok](reference-connect-ports.md) további információt.
* Ha a proxy vagy tűzfal korlátozása, mely URL-címek érhetők el, akkor az URL-címeket részletes ismertetését lásd: [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) kell megnyitni.
  * Ha a Microsoft Cloud németországi vagy a Microsoft Azure Government felhőben használ, tekintse meg [az Azure AD Connect szinkronizálási szolgáltatás példányai szempontok](reference-connect-instances.md) URL-címek.
* Az Azure AD Connect (1.1.614.0 verziót, és utána) alapértelmezés szerint használja a TLS 1.2 a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához. A TLS 1.2-es nem érhető el az alapul szolgáló operációs rendszer, ha az Azure AD Connect növekményes visszavált a régebbi protokollok (a TLS 1.1 és TLS 1.0-s).
* 1.1.614.0 verziónál régebbi az Azure AD Connect alapértelmezés szerint a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához használja a TLS 1.0-s. A TLS 1.2-es, kövesse a lépéseket a [engedélyezze a TLS 1.2 az Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Ha csatlakozik az internetre, az a következő beállítást használ egy kimenő proxy a **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** fájlt hozzá kell adni a telepítési varázsló és az Azure ad-ben A csatlakozni az internethez, és az Azure AD sync csatlakoztatása. Ez a szöveg, meg kell adni a fájl alján. Ebben a kódban &lt;PROXYADDRESS&gt; tényleges proxy IP-cím vagy a gazdagép nevét jelöli.

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

* Ha a proxykiszolgáló hitelesítést igényel, majd a [szolgáltatásfiók](reference-connect-accounts-permissions.md#adsync-service-account) található meg a tartományhoz, és adja meg a testre szabott beállításokat telepítési útvonalat kell használnia egy [egyéni szolgáltatásfiókot](how-to-connect-install-custom.md#install-required-components). Egy másik módosítás machine.config is szükséges. Az a Machine.config fájlban módosítása a telepítési varázsló és a szinkronizálási motor válaszol hitelesítési kéréseket a proxykiszolgálóhoz. Az összes telepítési varázsló oldalain, kivéve a **konfigurálása** lap az aláírt felhasználó hitelesítő adatait használja. Az a **konfigurálása** lapján a telepítési varázsló, a környezet végén át lett váltva a [szolgáltatásfiók](reference-connect-accounts-permissions.md#adsync-service-account) Ön által létrehozott. A machine.config szakaszban kell kinéznie.

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

* Amikor az Azure AD Connect webes kérelmet küld az Azure AD-címtár-szinkronizálás részeként, az Azure AD válaszolni akár 5 percet is igénybe vehet. Gyakori, hogy a kapcsolat üresjárati időkorlát konfigurálása a proxykiszolgálókra vonatkozó. Ellenőrizze, hogy a konfiguráció értéke legalább 6 percig vagy tovább is.

További információkért tekintse meg az MSDN a [alapértelmezett proxy elem](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
További információk, problémák adódnak a kapcsolódással, amikor: [csatlakozási problémák hibaelhárítása](tshoot-connect-connectivity.md).

### <a name="other"></a>Egyéb
* Nem kötelező: Egy tesztfelhasználói fiókja szinkronizálási ellenőrzése.

## <a name="component-prerequisites"></a>Összetevő-Előfeltételek
### <a name="powershell-and-net-framework"></a>PowerShell és a .NET-keretrendszer
Az Azure AD Connect a Microsoft PowerShell és a .NET-keretrendszer 4.5.1-es függ. Ez a verzió vagy újabb verzió van telepítve a kiszolgálón van szüksége. A Windows Server verziójától függően tegye a következőket:

* Windows Server 2012R2
  * A Microsoft PowerShell alapértelmezés szerint telepítve van. Nincs szükség műveletre.
  * .NET-keretrendszer 4.5.1-es vagy újabb Windows Update szolgáltatáson keresztül érhető el. Győződjön meg arról, hogy telepítette a legújabb frissítéseket a Windows Server, a Vezérlőpulton.
* Windows Server 2008 R2 és Windows Server 2012
  * A Microsoft PowerShell legújabb verziója érhető el a **Windows Management Framework 4.0**, elérhető [Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET-keretrendszer 4.5.1-es vagy újabb érhetők el a [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Engedélyezze a TLS 1.2-es, az Azure AD Connect
1.1.614.0 verziónál régebbi az Azure AD Connect alapértelmezés szerint a szinkronizálási motor kiszolgáló és az Azure AD közötti kommunikáció titkosításához használja a TLS 1.0-s. TLS 1.2 használatára a kiszolgálón alapértelmezés szerint a .NET-alkalmazások konfigurálásával módosíthatja. További információ a TLS 1.2 található [Microsoft biztonsági tanácsadó 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. A TLS 1.2-es nem lehet engedélyezni, előtt a Windows Server 2008 R2 vagy újabb. Ellenőrizze, hogy a .NET 4.5.1 gyorsjavítás telepítve van az operációs rendszer esetén lásd: [Microsoft biztonsági tanácsadó 2960358](https://technet.microsoft.com/security/advisory/2960358). Előfordulhat, hogy ez a gyorsjavítás vagy egy későbbi kiadásban már van telepítve a kiszolgálón.
2. Ha használja a Windows Server 2008 R2, majd győződjön meg arról, a TLS 1.2 engedélyezve van. A kiszolgáló Windows Server 2012 és újabb verzióiban a TLS 1.2 már engedélyezni kell.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. Az összes operációs rendszerek esetén állítsa ezt a beállításkulcsot, és indítsa újra a kiszolgálót.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Ha is engedélyezni szeretné a TLS 1.2 a szinkronizálási motor kiszolgáló és a egy távoli SQL Server között, akkor ellenőrizze, hogy rendelkezik a szükséges verziók telepítve [a TLS 1.2 támogatása a Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Összevonási telepítésének és konfigurálásának előfeltételei
### <a name="windows-remote-management"></a>Windows-távfelügyelet
Active Directory összevonási szolgáltatások vagy a webalkalmazás-Proxy üzembe helyezése az Azure AD Connect használatával, ha ezek a követelmények ellenőrzése:

* Ha a célkiszolgálót a tartományhoz, majd győződjön meg arról, hogy engedélyezve van-e a Windows távoli kezelése
  * Egy rendszergazda jogú PSH parancssori ablakban parancs használata `Enable-PSRemoting –force`
* A célkiszolgáló egy tartományhoz nem csatlakoztatott WAP-gép, akkor néhány további követelmények
  * A cél számítógépen (WAP gépen):
    * Győződjön meg, hogy a Rendszerfelügyeleti webszolgáltatások (Windows távoli felügyelet / WS-Management) szolgáltatás fut, a szolgáltatások beépülő modul használatával
    * Egy rendszergazda jogú PSH parancssori ablakban parancs használata `Enable-PSRemoting –force`
  * A gépen, amelyen a varázsló fut (Ha a célszámítógép nem tartományhoz kell csatlakozniuk, vagy nem megbízható tartomány):
    * Egy emelt szintű PSH parancssori ablakban a parancs használata `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * A Kiszolgálókezelőben:
      * szegélyhálózat (DMZ) WAP gazdagépet a gépkészlet (Kiszolgálókezelő -> kezelés -> kiszolgálók hozzáadása... DNS lapon)
      * A Kiszolgálókezelő minden kiszolgálók lap: kattintson jobb gombbal a WAP-kiszolgáló, és válassza ki a kezelés másként, adja meg a WAP gép helyi (nem tartományi) hitelesítő adatai
      * Távoli PSH a kapcsolat, a Kiszolgálókezelő minden kiszolgáló lap ellenőrzéséhez: kattintson jobb gombbal a WAP-kiszolgálóhoz, és válassza ki a Windows PowerShell. Távoli PSH munkamenet biztosításához hozhatók létre a távoli PowerShell-munkameneteket kell megnyitnia.

### <a name="ssl-certificate-requirements"></a>SSL-tanúsítvány követelményei
* Erősen ajánlott az AD FS farm összes csomópontja és az összes webalkalmazás-proxy kiszolgálók egy SSL tanúsítvány használható.
* A tanúsítványnak kell lennie egy X509 tanúsítvány.
* Használhatja az önaláírt tanúsítványt az összevonási kiszolgálók teszt laborkörnyezetben. Azonban az éles környezetben, azt javasoljuk, hogy beszerezte a tanúsítványt nyilvános hitelesítésszolgáltatótól.
  * Ha, amely nem nyilvánosan megbízható tanúsítványt használ, győződjön meg arról, hogy a webalkalmazás-Proxy kiszolgálókon telepített tanúsítvány megbízható, mind a helyi kiszolgálón, és minden összevonási kiszolgálón
* A tanúsítvány azonosítóját meg kell egyeznie az összevonási szolgáltatás nevét (például sts.contoso.com).
  * Az identitás vagy a tulajdonos alternatív nevére (SAN) kiterjesztése típus dNSName vagy, ha nincsenek SAN bejegyzések, a tulajdonos nevének kisbetűvel megadott.  
  * Több SAN-bejegyzés használható a tanúsítványban lévő egyik megegyezik az összevonási szolgáltatás neve.
  * Ha azt tervezi, munkahelyhez való, egy további SAN kötelező értékkel **enterpriseregistration.** a szervezet, például az egyszerű felhasználónév (UPN) utótag követ **enterpriseregistration.contoso.com**.
* Tanúsítványok alapján a CryptoAPI következő generációs (CNG) kulcsokat és a kulcstároló-szolgáltatók nem támogatottak. Ez azt jelenti, hogy a kriptográfiai Szolgáltató (kriptográfiai szolgáltató), és nem kulcstároló-Szolgáltatóra (kulcstároló-szolgáltató) alapuló tanúsítványt kell használnia.
* A rendszer támogatja a helyettesítő tanúsítványokat.

### <a name="name-resolution-for-federation-servers"></a>Az összevonási kiszolgálók névfeloldásra
* Állítsa be az AD FS összevonási szolgáltatás nevét (például sts.contoso.com) (a belső DNS-kiszolgáló) intranetes és extranetes (nyilvános DNS a tartományregisztráló keresztül) a DNS-rekordjait. Az intranetes DNS-rekordnál győződjön meg arról, hogy A rekordok, és nem a CNAME-rekordokat. Ez a windows-hitelesítés működéséhez a tartományhoz csatlakozó számítógépeken a szükséges.
* Ha egynél több AD FS-kiszolgálón vagy a webalkalmazás-Proxy kiszolgáló telepítését, majd győződjön meg arról, hogy konfigurálta a terheléselosztó, hogy a DNS-rekordok az AD FS összevonási szolgáltatás nevét (például sts.contoso.com) pont a terheléselosztóhoz.
* Integrált windows-hitelesítés használható a böngészőben megjelenő alkalmazásokba, az Internet Explorerben pedig az intranetre győződjön meg arról, hogy az AD FS összevonási szolgáltatás nevét (például sts.contoso.com) hozzáadódik az intranet zóna IE-ben. Ez csoportházirenddel szabályozott és a tartományhoz csatlakoztatott minden számítógépre központilag telepített.

## <a name="azure-ad-connect-supporting-components"></a>Az Azure AD Connect támogató összetevők
Az alábbiakban látható, amely a kiszolgálón, amelyen telepítve van-e az Azure AD Connect telepíti az Azure AD Connect összetevők listáját. A lista egy alapszintű expressz telepítéséhez. Ha egy másik SQL Server használata a szinkronizálási szolgáltatások telepítése lapon, majd az SQL Express LocalDB nincs telepítve helyben.

* Azure AD Connect Health
* A Microsoft SQL Server 2012 parancssori segédeszközök
* A Microsoft SQL Server 2012 Express LocalDB
* A Microsoft SQL Server 2012 natív ügyfél
* A Microsoft Visual C++ 2013 terjesztési csomag

## <a name="hardware-requirements-for-azure-ad-connect"></a>Az Azure AD Connect hardverkövetelményeit
Az alábbi táblázat az Azure AD Connect szinkronizálási számítógépre vonatkozó minimális követelményeknek.

| Az Active Directory-objektumok száma | CPU | Memory (Memória) | Merevlemez mérete |
| --- | --- | --- | --- |
| Kevesebb mint 10 000 |1,6 GHz-es |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz-es |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz-es |16 GB |100 GB |
| 100 000 vagy több objektumot az SQL Server teljes verziójának megadása kötelező | | | |
| 100,000–300,000 |1,6 GHz-es |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz-es |32 GB |450 GB |
| Több mint 600 000 |1,6 GHz-es |32 GB |500 GB |

Az AD FS- vagy webalkalmazás-kiszolgálón futó számítógépekre vonatkozó minimális követelmények a következő:

* CPU: Kettős alapvető 1,6 GHz-es vagy újabb
* MEMÓRIA: 2 GB-os vagy újabb
* Azure virtuális Gépen: Konfigurációs a2-es vagy újabb

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
