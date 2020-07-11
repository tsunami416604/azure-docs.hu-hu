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
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bd19093034b4427d9e1b637a653a90e0568cddf
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223924"
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
A Azure AD Connect kiszolgáló kritikus identitási adatait tartalmaz. Fontos, hogy a kiszolgálóhoz való rendszergazdai hozzáférés megfelelően legyen biztosítva, a [privilegizált hozzáférés biztonságossá tétele](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)című cikkben ismertetett irányelvek alapján. 

A Azure AD Connect-kiszolgálót 0. rétegű összetevőként kell kezelni, ahogy azt az [Active Directory felügyeleti modellben](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)dokumentálták.  

További információ a Active Directory környezet biztonságossá tételéről: [ajánlott eljárások a Active Directory biztonságossá tételéhez](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Telepítési előfeltételek 

- A Azure AD Connectt tartományhoz csatlakoztatott Windows Server 2012 vagy újabb rendszerre kell telepíteni. Erősen ajánlott, hogy ez a kiszolgáló egy tartományvezérlő. 
- Azure AD Connect nem telepíthető a Small Business Server vagy a Windows Server Essentials rendszerre, mielőtt 2019 (a Windows Server Essentials 2019 támogatott). A kiszolgálónak a Windows Server standard vagy magasabb szintűnek kell lennie.  
- A Azure AD Connect-kiszolgálónak teljes grafikus felhasználói felülettel kell rendelkeznie. A Azure AD Connect telepítése nem támogatott a Windows Server Core-on. 
- Ha Azure AD Connect varázslót használ az ADFS-konfiguráció felügyeletéhez, a Azure AD Connect kiszolgáló nem rendelkezhet a PowerShell átírásával Csoportházirend. Ha Azure AD Connect varázslót használ a szinkronizálási konfiguráció kezelésére, engedélyezheti a PowerShell átírását. 
- Ha Active Directory összevonási szolgáltatások (AD FS) üzembe helyezése folyamatban van, akkor: 
    - azok a kiszolgálók, amelyeken telepítve van a AD FS vagy a webalkalmazás-proxy, Windows Server 2012 R2 vagy újabb rendszernek kell lennie. A Távoli telepítéshez engedélyezni kell a Rendszerfelügyeleti webszolgáltatásokat ezeken a kiszolgálókon. 
    - konfigurálnia kell a TLS/SSL-tanúsítványokat.  Lásd: [SSL/TLS protokollok és titkosító csomagok kezelése a AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) és [az SSL-tanúsítványok kezelése a ad FSban](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - konfigurálnia kell a névfeloldást. 
- Ha a globális rendszergazdák rendelkeznek MFA-támogatással, akkor az URL-címnek szerepelnie kell https://secure.aadcdn.microsoftonline-p.com **must** a megbízható helyek listájában. A rendszer arra kéri, hogy adja hozzá ezt a helyet a megbízható helyek listájához, amikor a rendszer egy MFA-kihívást kér, és korábban még nem tette hozzá. Az Internet Explorer használatával adhatja hozzá a megbízható helyekhez.  

#### <a name="hardening-your-azure-ad-connect-server"></a>A Azure AD Connect-kiszolgáló megerősítése 
A Microsoft azt javasolja, hogy a Azure AD Connect kiszolgáló megerősítse a biztonsági támadási felületet az IT-környezet ezen kritikus összetevője számára. Az alábbi javaslatok segítenek enyhíteni a szervezete biztonsági kockázatait.

- A Azure AD Connect a tartományvezérlővel és más 0. szintű erőforrásokkal kell kezelnie:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material 
- A Azure AD Connect-kiszolgálóhoz való rendszergazdai hozzáférést csak tartományi rendszergazdák vagy más szigorúan ellenőrzött biztonsági csoportok számára kell korlátozni.
- Hozzon létre egy [dedikált fiókot az összes, emelt szintű hozzáféréssel rendelkező személy számára](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). A rendszergazdáknak nem szabad böngészni a weben, ellenőrizniük kell az e-mailjeit, és a napi hatékonyságnövelő feladatokat a magas jogosultsági szintű fiókokkal.
- Kövesse a [privilegizált hozzáférés biztonságossá tétele](https://docs.microsoft.com/windows-server/security/credentials-protection-and-management/how-to-configure-protected-accounts)című témakör útmutatását. 
- Győződjön meg arról, hogy minden gépnek egyedi helyi rendszergazdai jelszava van. A [helyi rendszergazda jelszavas megoldás (kör)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) egyedi véletlenszerű jelszavakat konfigurálhat az egyes munkaállomásokon, és a kiszolgálókat az ACL által védett Active Directory (ad) tárolja. Csak a jogosult jogosult felhasználók olvashatják el vagy kérhetik a helyi rendszergazdai fiók jelszavának visszaállítását. A munkaállomásokon és kiszolgálókon a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.)kérheti le a kört. A kör-és láb-környezetek üzemeltetéséhez szükséges további útmutatást a [tiszta forrás elve alapján a működési szabványok](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle)között találja. 
- Dedikált emelt [szintű hozzáférési munkaállomásokat (Paw)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) kell megvalósítani az összes munkatárs számára a szervezet információs rendszereihez való emelt szintű hozzáféréssel. 
- A Active Directory környezet támadási felületének csökkentése érdekében kövesse ezeket a [további irányelveket](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) .


### <a name="sql-server-used-by-azure-ad-connect"></a>Az Azure AD Connect által használt SQL-kiszolgáló
* Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Alapértelmezés szerint a SQL Server 2012 Express LocalDB (SQL Server Express) egy egyszerűsített verziója van telepítve. A SQL Server Express 10 GB méretű korláttal rendelkezik, amely lehetővé teszi körülbelül 100 000 objektum kezelését. Ha nagyobb mennyiségű címtár-objektumot kell kezelnie, a telepítővarázslót a SQL Server egy másik telepítésére kell irányítani. A SQL Server telepítésének típusa hatással lehet [Azure ad Connect teljesítményére](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Ha a SQL Server eltérő telepítését használja, a következő követelmények érvényesek:
  * A Azure AD Connect a Microsoft SQL Server összes verzióját támogatja a 2012 (a legújabb szervizcsomaggal) SQL Server 2019. A Microsoft Azure SQL Database adatbázisként **nem támogatott** .
  * Kis-és nagybetűket nem megkülönböztető SQL-rendezést kell használnia. Ezeket a rendezéseket a \_ nevükben CI_ azonosítjuk. A kis-és nagybetűket megkülönböztető rendezés használata **nem támogatott** a \_ nevükben CS_ azonosítva.
  * SQL-példányon csak egy szinkronizálási motor tartozhat. **Nem támogatott** SQL-példányok megosztása FIM/a rendszerbe történő szinkronizálással, vagy a következővel: Azure ad-szinkronizáló.

### <a name="accounts"></a>Fiókok
* Egy Azure AD globális rendszergazdai fiók az Azure AD-bérlőhöz, amelybe integrálni kíván. Ennek a fióknak **iskolai vagy szervezeti fióknak** kell lennie, és nem lehet **Microsoft-fiók**.
* Ha az [expressz beállításokat](reference-connect-accounts-permissions.md#express-settings-installation) használja, vagy a frissítését a (z) rendszerről, akkor vállalati rendszergazdai fiókkal kell rendelkeznie a helyszíni Active Directoryhoz.
* Ha az egyéni beállítások telepítési útvonalát használja, több lehetőség is rendelkezésre áll. További információ: [egyéni telepítési beállítások](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Kapcsolat
* A Azure AD Connect-kiszolgálónak az intraneten és az interneten egyaránt DNS-feloldásra van szüksége. A DNS-kiszolgálónak képesnek kell lennie a nevek feloldására a helyszíni Active Directory és az Azure AD-végpontokon.
* Ha tűzfallal rendelkezik az intraneten, és meg kell nyitnia a portokat a Azure AD Connect-kiszolgálók és a tartományvezérlők között, további információért lásd: [Azure ad Connect portok](reference-connect-ports.md) .
* Ha a proxy vagy a tűzfal korlátozza, hogy mely URL-címek érhetők el, akkor meg kell nyitni az [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) által dokumentált URL-címeket.
  * Ha a Microsoft Cloudt Németországban vagy a Microsoft Azure Government felhőben használja, tekintse meg [Azure ad Connect szinkronizálási szolgáltatás példányainak szempontjait](reference-connect-instances.md) URL-címeknél.
* A Azure AD Connect (1.1.614.0 és újabb verzió) alapértelmezés szerint a TLS 1,2-et használja a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához. Ha a TLS 1,2 nem érhető el az alapul szolgáló operációs rendszeren, Azure AD Connect fokozatosan visszakerül a régebbi protokollokra (TLS 1,1 és TLS 1,0).
* A 1.1.614.0 verzió előtt a Azure AD Connect alapértelmezés szerint a TLS 1,0-et használja a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához. A TLS 1,2-re való váltáshoz kövesse a [tls 1,2 engedélyezése a Azure ad Connect számára](#enable-tls-12-for-azure-ad-connect)című témakör lépéseit.
* Ha kimenő proxyt használ az internethez való csatlakozáshoz, akkor a **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** fájl következő beállítását hozzá kell adni a telepítővarázsló számára, és Azure ad Connect a szinkronizálást, hogy csatlakozni tudjon az internethez és az Azure ad-hoz. Ezt a szöveget a fájl alján kell megadni. Ebben a kódban &lt; a PROXYADDRESS &gt; a tényleges proxy IP-címét vagy állomásnevét jelöli.

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

* Ha a proxykiszolgáló hitelesítést igényel, akkor a [szolgáltatási fióknak](reference-connect-accounts-permissions.md#adsync-service-account) a tartományban kell lennie, és a testreszabott beállítások telepítési útvonalát kell használnia [Egyéni szolgáltatásfiók](how-to-connect-install-custom.md#install-required-components)megadásához. A machine.config módosítására is szükség van. Ha ez a változás machine.config, a telepítővarázsló és a Szinkronizáló motor válaszol a proxykiszolgáló hitelesítési kéréseire. A telepítővarázsló összes lapján, a configure ( **Konfigurálás** ) lap kivételével a rendszer a bejelentkezett felhasználó hitelesítő adatait használja. A telepítővarázsló végén a **configure (Konfigurálás** ) lapon a környezet átvált az Ön által létrehozott [szolgáltatásfiók](reference-connect-accounts-permissions.md#adsync-service-account) -ra. A machine.config szakasznak így kell kinéznie.

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
  * A Microsoft PowerShell alapértelmezés szerint telepítve van. Semmit nem kell tenni.
  * A .NET-keretrendszer 4.5.1-es és újabb kiadásai a Windows Updateon keresztül érhetők el. Győződjön meg arról, hogy a Vezérlőpulton telepítette a legújabb frissítéseket a Windows Server rendszerre.
* Windows Server 2012
  * A Microsoft PowerShell legújabb verziója a **Windows Management Framework 4,0**-es verziójában érhető el, amely a [Microsoft letöltőközpontból](https://www.microsoft.com/downloads)érhető el.
  * A .NET-keretrendszer 4.5.1-es és újabb kiadásai a [Microsoft letöltőközpontban](https://www.microsoft.com/downloads)érhetők el.


### <a name="enable-tls-12-for-azure-ad-connect"></a>A TLS 1,2 engedélyezése Azure AD Connect
A 1.1.614.0 verzió előtt a Azure AD Connect alapértelmezés szerint TLS 1,0-et használ a Sync Engine-kiszolgáló és az Azure AD közötti kommunikáció titkosításához. Ezt úgy változtathatja meg, hogy a .NET-alkalmazások alapértelmezés szerint a (z) kiszolgálón a TLS 1,2 használatára vannak konfigurálva. A TLS 1,2-ről a [Microsoft Security advisor 2960358 webhelyén](https://technet.microsoft.com/security/advisory/2960358)talál további információt.

1.  Győződjön meg arról, hogy telepítve van az operációs rendszerének megfelelő .NET 4.5.1-gyorsjavítás, lásd: [Microsoft biztonsági tanácsadó 2960358](https://technet.microsoft.com/security/advisory/2960358). Lehet, hogy ez a gyorsjavítás vagy egy későbbi kiadás már telepítve van a kiszolgálón.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \. NETFramework\v4.0.30319 "alatt" = DWORD: 00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
