---
title: 'Azure AD Connect: előfeltételek és hardver | Microsoft Docs'
description: Ez a témakör ismerteti az előfeltételeket és a Azure AD Connectéhez szükséges hardverkövetelmények
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
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d54ef06fd63a1064962aea6099a2289d04ff658
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462016"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Az Azure AD Connect előfeltételei
Ez a témakör ismerteti az előfeltételeket és a Azure AD Connect hardverre vonatkozó követelményeit.

## <a name="before-you-install-azure-ad-connect"></a>A Azure AD Connect telepítése előtt
A Azure AD Connect telepítése előtt néhány dolog szükséges.

### <a name="azure-ad"></a>Azure AD
* Egy Azure AD-bérlő. Egy [ingyenes Azure-próbaverziót](https://azure.microsoft.com/pricing/free-trial/)kap. Azure AD Connect kezeléséhez a következő portálok egyikét használhatja:
  * A [Azure Portal](https://portal.azure.com).
  * Az [Office-portálon](https://portal.office.com).  
* [Adja hozzá és ellenőrizze az](../active-directory-domains-add-azure-portal.md) Azure ad-ben használni kívánt tartományt. Ha például contoso.com kíván használni a felhasználók számára, akkor győződjön meg arról, hogy a tartomány ellenőrzése megtörtént, és nem csak a contoso.onmicrosoft.com alapértelmezett tartományát használja.
* Az Azure AD-bérlők alapértelmezés szerint 50 000 objektumot is lehetővé tesznek. A tartomány ellenőrzésekor a korlát a 300k-objektumokra nő. Ha még több objektumra van szüksége az Azure AD-ben, akkor meg kell nyitnia egy támogatási esetet, hogy a korlát még tovább is megnövekszik. Ha 500k-nál több objektumra van szüksége, akkor szüksége lesz egy licencre, például az Office 365, alapszintű Azure AD, prémium szintű Azure AD vagy a nagyvállalati mobilitásra és a biztonságra.

### <a name="prepare-your-on-premises-data"></a>A helyszíni adatfeldolgozás előkészítése
* Az Azure AD-hez és az Office 365-hoz való szinkronizálás előtt a [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) segítségével azonosíthatja a hibákat, például az ismétlődéseket és a formázási problémákat a címtárban.
* Tekintse át [Az Azure ad-ben engedélyezhető választható szinkronizálási funkciókat](how-to-connect-syncservice-features.md) , és értékelje ki, hogy mely szolgáltatásokat kell engedélyezni.

### <a name="on-premises-active-directory"></a>Helyszíni Active Directory
* Az AD-séma verziójának és az erdő működési szintjének Windows Server 2003 vagy újabb verziójúnak kell lennie. A tartományvezérlők bármilyen verziót futtathatnak, amíg a séma-és erdő szintű követelmények teljesülnek.
* Ha azt tervezi, hogy a szolgáltatás **jelszava visszaírási**használja, akkor a tartományvezérlőknek Windows Server 2008 R2 vagy újabb rendszeren kell lenniük.
* Az Azure AD által használt tartományvezérlőnek írhatónak kell lennie. ÍRÁSVÉDETT tartományvezérlő használata **nem támogatott** , és a Azure ad Connect nem hajtja végre az írási átirányítást.
* A helyszíni erdők/tartományok használata **nem támogatott** a "pontozott" értékkel (a név egy pontot tartalmaz: ".") NetBios-nevek.
* Javasoljuk, hogy [engedélyezze a Active Directory Lomtárát](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Azure AD Connect kiszolgáló
>[!IMPORTANT]
>A Azure AD Connect kiszolgáló kritikus identitási adatokból áll, és 0. rétegű összetevőként kell kezelni, ahogy azt [az Active Directory felügyeleti csomag modelljében](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) dokumentálták

* Azure AD Connect nem telepíthető a Small Business Server vagy a Windows Server Essentials rendszerre, mielőtt 2019 (a Windows Server Essentials 2019 támogatott). A kiszolgálónak a Windows Server standard vagy magasabb szintűnek kell lennie.
* A Azure AD Connect tartományvezérlőre történő telepítése nem ajánlott biztonsági eljárások és szigorúbb beállítások miatt, amelyek megakadályozzák, hogy a Azure AD Connect megfelelően legyenek telepítve.
* A Azure AD Connect-kiszolgálónak teljes grafikus felhasználói felülettel kell rendelkeznie. A Server Core-on való telepítés **nem támogatott** .
>[!IMPORTANT]
>A Azure AD Connect a Small Business Server, a Server Essentials vagy a Server Core rendszerre való telepítése nem támogatott.

* Azure AD Connect a Windows Server 2008 R2 vagy újabb verzióra kell telepíteni. A kiszolgálónak tartományhoz kell csatlakoznia, és lehet tartományvezérlő vagy tagkiszolgáló.
* Ha a Azure AD Connect Windows Server 2008 R2 rendszerre telepíti, akkor ügyeljen arra, hogy a legújabb gyorsjavításokat alkalmazza Windows Updateról. A telepítés nem indítható el egy kijavítás nélküli kiszolgálóval.
* Ha a szolgáltatás **jelszavas szinkronizálását**tervezi használni, akkor a Azure ad Connect-kiszolgálónak a Windows Server 2008 R2 SP1 vagy újabb verziójának kell lennie.
* Ha egy **csoportosan felügyelt szolgáltatásfiókot**kíván használni, akkor a Azure ad Connect-kiszolgálónak a Windows Server 2012-es vagy újabb verziójával kell rendelkeznie.
* A Azure AD Connect kiszolgálónak a [.NET-keretrendszer 4.5.1](#component-prerequisites) -es vagy újabb verziójával, valamint a [Microsoft PowerShell 3,0](#component-prerequisites) -es vagy újabb verziójával kell rendelkeznie.
* Ha Azure AD Connect varázslót használ az ADFS-konfiguráció felügyeletéhez, a Azure AD Connect kiszolgáló nem rendelkezhet a PowerShell átírásával Csoportházirend. Ha Azure AD Connect varázslót használ a szinkronizálási konfiguráció kezelésére, engedélyezheti a PowerShell átírását.
* Ha Active Directory összevonási szolgáltatások (AD FS) üzembe helyezése folyamatban van, azok a kiszolgálók, amelyeken AD FS vagy webalkalmazás-proxy telepítve van, Windows Server 2012 R2 vagy újabb rendszernek kell lennie. A Távoli telepítéshez [engedélyezni kell a](#windows-remote-management) Rendszerfelügyeleti webszolgáltatásokat ezeken a kiszolgálókon.
* Ha Active Directory összevonási szolgáltatások (AD FS) üzembe helyezése folyamatban van, [SSL-tanúsítványokra](#ssl-certificate-requirements)van szükség.
* Ha Active Directory összevonási szolgáltatások (AD FS) üzembe helyezése folyamatban van, akkor konfigurálnia kell [a névfeloldást](#name-resolution-for-federation-servers).
* Ha a globális rendszergazdák rendelkeznek MFA-támogatással, akkor a **https://secure.aadcdn.microsoftonline-p.com** URL-címnek a megbízható helyek listájában kell lennie. A rendszer arra kéri, hogy adja hozzá ezt a helyet a megbízható helyek listájához, amikor a rendszer egy MFA-kihívást kér, és korábban még nem tette hozzá. Az Internet Explorer használatával adhatja hozzá a megbízható helyekhez.
* A Microsoft azt javasolja, hogy a Azure AD Connect kiszolgáló megerősítse a biztonsági támadási felületet az IT-környezet ezen kritikus összetevője számára.  Az alábbi ajánlásokat követve csökkentheti a szervezete biztonsági kockázatait.

* Azure AD Connect üzembe helyezése egy tartományhoz csatlakoztatott kiszolgálón, és a rendszergazdai hozzáférés korlátozása a tartományi rendszergazdák vagy más szigorúan ellenőrzött biztonsági csoportok számára.

További tudnivalókért lásd: 

* [Rendszergazdák csoportok biztonságossá tétele](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Beépített rendszergazdai fiókok biztonságossá tétele](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Biztonsági fejlesztés és fenntartás a támadási felületek csökkentésével](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [A Active Directory támadási felület csökkentése](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>A Azure AD Connect által használt SQL Server
* Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Alapértelmezés szerint a SQL Server 2012 Express LocalDB (SQL Server Express) egy egyszerűsített verziója van telepítve. A SQL Server Express 10 GB méretű korláttal rendelkezik, amely lehetővé teszi körülbelül 100 000 objektum kezelését. Ha nagyobb mennyiségű címtár-objektumot kell kezelnie, a telepítővarázslót a SQL Server egy másik telepítésére kell irányítani. A SQL Server telepítésének típusa hatással lehet [Azure ad Connect teljesítményére](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Ha a SQL Server eltérő telepítését használja, a következő követelmények érvényesek:
  * A Azure AD Connect a Microsoft SQL Server összes verzióját támogatja a 2008 R2-ről (a legújabb szervizcsomaggal) a SQL Server 2019. A Microsoft Azure SQL Database adatbázisként **nem támogatott** .
  * Kis-és nagybetűket nem megkülönböztető SQL-rendezést kell használnia. Ezeket a rendezéseket a nevükben egy \_CI_ azonosítja. **Nem támogatott** a kis-és nagybetűket megkülönböztető rendezés használata, amelyet \_CS_ azonosít a nevükben.
  * SQL-példányon csak egy szinkronizálási motor tartozhat. **Nem támogatott** SQL-példányok megosztása FIM/a rendszerbe történő szinkronizálással, vagy a következővel: Azure ad-szinkronizáló.

### <a name="accounts"></a>Fiókok
* Egy Azure AD globális rendszergazdai fiók az Azure AD-bérlőhöz, amelybe integrálni kíván. Ennek a fióknak **iskolai vagy szervezeti fióknak** kell lennie, és nem lehet **Microsoft-fiók**.
* Ha az expressz beállításokat használja, vagy a frissítését a (z) rendszerről, akkor vállalati rendszergazdai fiókkal kell rendelkeznie a helyszíni Active Directoryhoz.
* [Active Directory fiókok,](reference-connect-accounts-permissions.md) ha az egyéni beállítások telepítési útvonalát vagy egy vállalati rendszergazdai fiókot használ a helyszíni Active Directoryhoz.

### <a name="connectivity"></a>Kapcsolat
* A Azure AD Connect-kiszolgálónak az intraneten és az interneten egyaránt DNS-feloldásra van szüksége. A DNS-kiszolgálónak képesnek kell lennie a nevek feloldására a helyszíni Active Directory és az Azure AD-végpontokon.
* Ha tűzfallal rendelkezik az intraneten, és meg kell nyitnia a portokat a Azure AD Connect-kiszolgálók és a tartományvezérlők között, további információért lásd: [Azure ad Connect portok](reference-connect-ports.md) .
* Ha a proxy vagy a tűzfal korlátozza, hogy mely URL-címek érhetők el, akkor meg kell nyitni az [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) által dokumentált URL-címeket.
  * Ha a Microsoft Cloudt Németországban vagy a Microsoft Azure Government felhőben használja, tekintse meg [Azure ad Connect szinkronizálási szolgáltatás példányainak szempontjait](reference-connect-instances.md) URL-címeknél.
* A Azure AD Connect (1.1.614.0 és újabb verzió) alapértelmezés szerint a TLS 1,2-et használja a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához. Ha a TLS 1,2 nem érhető el az alapul szolgáló operációs rendszeren, Azure AD Connect fokozatosan visszakerül a régebbi protokollokra (TLS 1,1 és TLS 1,0).
* A 1.1.614.0 verzió előtt a Azure AD Connect alapértelmezés szerint a TLS 1,0-et használja a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához. A TLS 1,2-re való váltáshoz kövesse a [tls 1,2 engedélyezése a Azure ad Connect számára](#enable-tls-12-for-azure-ad-connect)című témakör lépéseit.
* Ha kimenő proxyt használ az internethez való csatlakozáshoz, a **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** fájl következő beállítását hozzá kell adni a telepítővarázsló számára, és Azure ad Connect a szinkronizálást, hogy csatlakozni tudjon az internethez és az Azure ad-hoz. Ezt a szöveget a fájl alján kell megadni. Ebben a kódban &lt;PROXYADDRESS&gt; a tényleges proxy IP-címét vagy állomásnevét jelöli.

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

* Ha a proxykiszolgáló hitelesítést igényel, akkor a [szolgáltatási fióknak](reference-connect-accounts-permissions.md#adsync-service-account) a tartományban kell lennie, és a testreszabott beállítások telepítési útvonalát kell használnia [Egyéni szolgáltatásfiók](how-to-connect-install-custom.md#install-required-components)megadásához. Szükség van a Machine. config fájl másik módosítására is. Ha ezt a változást a Machine. config fájlban módosítja, a telepítővarázsló és a Szinkronizáló motor válaszol a proxykiszolgáló hitelesítési kéréseire. A telepítővarázsló összes lapján, a configure ( **Konfigurálás** ) lap kivételével a rendszer a bejelentkezett felhasználó hitelesítő adatait használja. A telepítővarázsló végén a **configure (Konfigurálás** ) lapon a környezet átvált az Ön által létrehozott [szolgáltatásfiók](reference-connect-accounts-permissions.md#adsync-service-account) -ra. A Machine. config szakasznak így kell kinéznie.

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

* Ha Azure AD Connect webes kérést küld az Azure AD-nak a címtár-szinkronizálás részeként, az Azure AD akár 5 percet is igénybe vehet. Gyakori, hogy a proxykiszolgálók a kapcsolat tétlen időtúllépési konfigurációját. Győződjön meg arról, hogy a konfiguráció legalább 6 percre van beállítva.

További információ: MSDN az [alapértelmezett proxy elemről](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
További információt a kapcsolattal kapcsolatos problémákkal kapcsolatban a [kapcsolódási problémák elhárítása](tshoot-connect-connectivity.md)című témakörben talál.

### <a name="other"></a>Egyéb
* Nem kötelező: tesztelési felhasználói fiók a szinkronizálás ellenőrzéséhez.

## <a name="component-prerequisites"></a>Összetevők előfeltételei
### <a name="powershell-and-net-framework"></a>PowerShell és .NET-keretrendszer
A Azure AD Connect a Microsoft PowerShelltől és a .NET-keretrendszer 4.5.1-től függ. Erre a verzióra vagy egy újabb, a kiszolgálóra telepített verzióra van szükség. A Windows Server-verziótól függően tegye a következőket:

* Windows Server 2012R2
  * A Microsoft PowerShell alapértelmezés szerint telepítve van. Nincs szükség műveletre.
  * A .NET-keretrendszer 4.5.1-es és újabb kiadásai a Windows Updateon keresztül érhetők el. Győződjön meg arról, hogy a Vezérlőpulton telepítette a legújabb frissítéseket a Windows Server rendszerre.
* Windows Server 2008 R2 és Windows Server 2012
  * A Microsoft PowerShell legújabb verziója a **Windows Management Framework 4,0**-es verziójában érhető el, amely a [Microsoft letöltőközpontból](https://www.microsoft.com/downloads)érhető el.
  * A .NET-keretrendszer 4.5.1-es és újabb kiadásai a [Microsoft letöltőközpontban](https://www.microsoft.com/downloads)érhetők el.


### <a name="enable-tls-12-for-azure-ad-connect"></a>A TLS 1,2 engedélyezése Azure AD Connect
A 1.1.614.0 verzió előtt a Azure AD Connect alapértelmezés szerint TLS 1,0-et használ a Sync Engine-kiszolgáló és az Azure AD közötti kommunikáció titkosításához. Ezt úgy változtathatja meg, hogy a .NET-alkalmazások alapértelmezés szerint a (z) kiszolgálón a TLS 1,2 használatára vannak konfigurálva. A TLS 1,2-ről a [Microsoft Security advisor 2960358 webhelyén](https://technet.microsoft.com/security/advisory/2960358)talál további információt.

1. A TLS 1,2 nem engedélyezhető a Windows Server 2008 R2 vagy korábbi verziók előtt. Győződjön meg arról, hogy telepítve van az operációs rendszerének megfelelő .NET 4.5.1-gyorsjavítás, lásd: [Microsoft biztonsági tanácsadó 2960358](https://technet.microsoft.com/security/advisory/2960358). Lehet, hogy ez a gyorsjavítás vagy egy későbbi kiadás már telepítve van a kiszolgálón.
2. Ha a Windows Server 2008 R2 rendszert használja, akkor győződjön meg arról, hogy a TLS 1,2 engedélyezve van. A Windows Server 2012 Server és újabb verziókban a TLS 1,2 már engedélyezve kell lennie.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. Az összes operációs rendszer esetében állítsa be ezt a beállításkulcsot, és indítsa újra a kiszolgálót.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Ha a TLS 1,2-et is engedélyezni szeretné a Sync Engine-kiszolgáló és egy távoli SQL Server között, akkor győződjön meg arról, hogy a szükséges verziók telepítve vannak a [tls 1,2-támogatáshoz a Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Az összevonás telepítésének és konfigurálásának előfeltételei
### <a name="windows-remote-management"></a>Rendszerfelügyeleti webszolgáltatások
Ha a Azure AD Connect használatával telepíti Active Directory összevonási szolgáltatások (AD FS) vagy a webalkalmazás-proxyt, olvassa el a következő követelményeket:

* Ha a célkiszolgáló tartományhoz van csatlakoztatva, ellenőrizze, hogy engedélyezve van-e a Windows távoli felügyelt
  * Emelt szintű PSH parancssori ablakban használja a parancsot `Enable-PSRemoting –force`
* Ha a célkiszolgáló nem tartományhoz csatlakozó WAP-gép, akkor néhány további követelmény
  * A célszámítógépen (WAP-gép):
    * Győződjön meg arról, hogy a Rendszerfelügyeleti webszolgáltatások (Windows távfelügyeleti/WS-Management) szolgáltatás fut a szolgáltatások beépülő modulon keresztül.
    * Emelt szintű PSH parancssori ablakban használja a parancsot `Enable-PSRemoting –force`
  * Azon a számítógépen, amelyen a varázsló fut (ha a célszámítógép nem tartományhoz csatlakoztatott vagy nem megbízható tartomány):
    * Emelt szintű PSH parancssori ablakban használja a parancsot `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * A Kiszolgálókezelőben:
      * DMZ WAP-gazdagép hozzáadása a számítógép-készlethez (Kiszolgálókezelő – > kezelés – > kiszolgálók hozzáadása... DNS lap használata)
      * Kiszolgálókezelő minden kiszolgáló lap: kattintson a jobb gombbal a WAP-kiszolgáló elemre, és válassza a kezelés másként... lehetőséget, írja be a WAP-gép helyi (nem tartományi) jogcímeit.
      * A távoli PSH-kapcsolat ellenőrzéséhez a Kiszolgálókezelő minden kiszolgáló lapján kattintson a jobb gombbal a WAP-kiszolgáló lehetőségre, és válassza a Windows PowerShell elemet. A távoli PSH-munkamenetnek nyitva kell lennie a távoli PowerShell-munkamenetek létrehozásához.

### <a name="ssl-certificate-requirements"></a>SSL-tanúsítványokra vonatkozó követelmények
* Javasoljuk, hogy ugyanazt az SSL-tanúsítványt használja a AD FS Farm és az összes webalkalmazás-proxy kiszolgáló összes csomópontján.
* A tanúsítványnak X509-tanúsítványnak kell lennie.
* Az összevonási kiszolgálókon önaláírt tanúsítványt is használhat tesztkörnyezetben tesztkörnyezetben. Éles környezetben azonban azt javasoljuk, hogy egy nyilvános HITELESÍTÉSSZOLGÁLTATÓTÓL szerezze be a tanúsítványt.
  * Ha olyan tanúsítványt használ, amely nem nyilvánosan megbízható, győződjön meg arról, hogy az egyes webalkalmazás-proxy kiszolgálókon telepített tanúsítvány megbízható a helyi kiszolgálón és az összes összevonási kiszolgálón is.
* A tanúsítvány identitásának meg kell egyeznie az összevonási szolgáltatás nevével (például sts.contoso.com).
  * Az identitás vagy a tulajdonos alternatív neve (SAN) dNSName típusú bővítmény, vagy ha nincsenek SAN-bejegyzések, a tulajdonos neve köznapi névként van megadva.  
  * Több SAN-bejegyzés is szerepelhet a tanúsítványban, ha ezek egyike megegyezik az összevonási szolgáltatás nevével.
  * Ha Workplace Join használatát tervezi, további SAN-t kell megadnia a enterpriseregistration értékkel **.** ezt követően a szervezet egyszerű felhasználóneve (UPN) utótagja, például **enterpriseregistration.contoso.com**.
* A CryptoAPI következő generációs (CNG) kulcsokon és a kulcstároló-szolgáltatókon alapuló tanúsítványok nem támogatottak. Ez azt jelenti, hogy tanúsítványt kell használnia egy CSP (kriptográfiai szolgáltató) alapján, és nem KSP (kulcstároló-szolgáltató).
* A Wild-Card tanúsítványok támogatottak.

### <a name="name-resolution-for-federation-servers"></a>Névfeloldás összevonási kiszolgálókon
* Állítsa be a DNS-rekordokat a AD FS összevonási szolgáltatás neveként (például sts.contoso.com) az intraneten (belső DNS-kiszolgáló) és az extraneten (a nyilvános DNS-en keresztül). Az intranetes DNS-rekord esetében ügyeljen arra, hogy a rekordokat és a nem CNAME rekordokat használja. Ez ahhoz szükséges, hogy a Windows-hitelesítés megfelelően működjön a tartományhoz csatlakoztatott gépről.
* Ha egynél több AD FS-kiszolgálót vagy webalkalmazás-proxykiszolgálót telepít, győződjön meg arról, hogy konfigurálta a terheléselosztó nevét, és hogy a AD FS összevonási szolgáltatás neve (például sts.contoso.com) DNS-rekordjai a terheléselosztó felé mutatnak.
* Ahhoz, hogy a Windows beépített hitelesítése az Internet Explorer használatával működjön az intraneten, győződjön meg arról, hogy a AD FS összevonási szolgáltatás neve (például sts.contoso.com) hozzá van adva az az intranetes zónához az IE-ben. Ez a csoportházirenden keresztül vezérelhető, és az összes tartományhoz csatlakoztatott számítógépen üzembe helyezhető.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect támogató összetevők
Az alábbi lista azon összetevők listáját tartalmazza, amelyeket Azure AD Connect a Azure AD Connect telepítésére szolgáló kiszolgálón. Ez a lista egy alapszintű expressz telepítéshez használható. Ha más SQL Server használatát választja a szinkronizálási szolgáltatások telepítése lapon, akkor az SQL Express LocalDB nincs helyileg telepítve.

* Azure AD Connect Health
* Microsoft SQL Server 2012 parancssori segédeszközök
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 natív ügyfél
* Microsoft Visual C++ 2013 újraterjesztési csomag

## <a name="hardware-requirements-for-azure-ad-connect"></a>A Azure AD Connecthez szükséges hardverkövetelmények
Az alábbi táblázat a Azure AD Connect szinkronizálására szolgáló számítógép minimális követelményeit mutatja be.

| Objektumok száma a Active Directoryban | CPU | Memory (Memória) | Merevlemez mérete |
| --- | --- | --- | --- |
| Kevesebb mint 10 000 |1,6 GHz-es |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz-es |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz-es |16 GB |100 GB |
| 100 000 vagy több objektum esetén a SQL Server teljes verziója szükséges | | | |
| 100,000–300,000 |1,6 GHz-es |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz-es |32 GB |450 GB |
| Több mint 600 000 |1,6 GHz-es |32 GB |500 GB |

AD FS vagy webalkalmazás-proxy kiszolgálókat futtató számítógépek minimális követelményei a következők:

* CPU: kétmagos 1,6 GHz-es vagy újabb
* MEMÓRIA: 2 GB vagy magasabb
* Azure-beli virtuális gép: a2 vagy újabb konfiguráció

## <a name="next-steps"></a>További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
