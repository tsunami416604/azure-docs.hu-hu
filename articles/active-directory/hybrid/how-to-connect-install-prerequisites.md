---
title: 'Azure AD Connect: Előfeltételek és hardver | Microsoft dokumentumok'
description: Ez a témakör ismerteti az Azure AD Connect előfeltételeit és hardverkövetelményeit
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
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6446b039d90e04c9fe7fca28b361f620183a0292
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875741"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Az Azure AD Connect előfeltételei
Ez a témakör ismerteti az Azure AD Connect előfeltételeit és hardverkövetelményeit.

## <a name="before-you-install-azure-ad-connect"></a>Az Azure AD Connect telepítése előtt
Az Azure AD Connect telepítése előtt néhány dologra szüksége van.

### <a name="azure-ad"></a>Azure AD
* Egy Azure AD-bérlő. Az [Azure ingyenes próbaverziója ingyenes.](https://azure.microsoft.com/pricing/free-trial/) Az alábbi portálok egyikével kezelheti az Azure AD Connectet:
  * Az [Azure portal](https://portal.azure.com).
  * Az [Office portálon](https://portal.office.com).  
* [Adja hozzá, és ellenőrizze a tartományt](../active-directory-domains-add-azure-portal.md) használni az Azure AD.Add and verify the domain you you plan to use in Azure AD. Ha például contoso.com kíván használni a felhasználók számára, akkor győződjön meg arról, hogy ez a tartomány ellenőrizve lett, és nem csak az contoso.onmicrosoft.com alapértelmezett tartományt használja.
* Az Azure AD-bérlő alapértelmezés szerint 50 k objektumok lehetővé teszi. A tartomány ellenőrzése kor a korlát 300 ezer objektumra nő. Ha még több objektumra van szüksége az Azure AD-ben, akkor meg kell nyitnia egy támogatási esetet, hogy a korlát még tovább növekedjen. Ha több mint 500 ezer objektumra van szüksége, akkor licencre van szüksége, például office 365-re, Azure AD Basicre, Azure AD Premiumra vagy Enterprise Mobility and Security-re.

### <a name="prepare-your-on-premises-data"></a>A helyszíni adatok előkészítése
* Az [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) segítségével azonosíthatja az Olyan hibákat, mint az ismétlődések és a formázási problémák a címtárban, mielőtt szinkronizálna az Azure AD-vel és az Office 365-tel.
* Tekintse át [az Azure AD-ben engedélyezhető választható szinkronizálási funkciókat,](how-to-connect-syncservice-features.md) és értékelje, hogy mely funkciókat kell engedélyeznie.

### <a name="on-premises-active-directory"></a>Helyszíni Active Directory
* Az AD-séma verziónak és az erdő működési szintjének Windows Server 2003 vagy újabb rendszernek kell lennie. A tartományvezérlők bármilyen verziót futtathatnak, amíg a séma- és erdőszintű követelmények teljesülnek.
* Ha a szolgáltatás **jelszó-visszaírását**tervezi használni, akkor a tartományvezérlőknek Windows Server 2008 R2 vagy újabb rendszeren kell lenniük.
* Az Azure AD által használt tartományvezérlőnek írhatónak kell lennie. Írásvédett tartományvezérlő (írásvédett tartományvezérlő) használata **nem támogatott,** és az Azure AD Connect nem követi az írási átirányításokat.
* Nem **támogatott** a helyszíni erdők/tartományok használata a "pontozott" használatával (a név "" pontot tartalmaz.") NetBios nevek.
* Az Active [Directory lomtár engedélyezése](how-to-connect-sync-recycle-bin.md)ajánlott.

### <a name="azure-ad-connect-server"></a>Azure AD Connect kiszolgáló
>[!IMPORTANT]
>Az Azure AD Connect-kiszolgáló kritikus identitásadatokat tartalmaz, és az [Active Directory felügyeleti réteg modelljében](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) dokumentált 0.

* Az Azure AD Connect nem telepíthető a Small Business Server vagy a Windows Server Essentials 2019 előtt (a Windows Server Essentials 2019 támogatott). A kiszolgálónak windows Server szabványt vagy annál jobb at kell használnia.
* Az Azure AD Connect tartományvezérlőre történő telepítése nem ajánlott a biztonsági eljárások és a szigorúbb beállítások miatt, amelyek megakadályozhatják az Azure AD Connect megfelelő telepítését.
* Az Azure AD Connect kiszolgálónak teljes gui-val kell rendelkeznie. A kiszolgálómagra való telepítés **nem támogatott.**
>[!IMPORTANT]
>Az Azure AD Connect telepítése kisvállalati kiszolgálóra, kiszolgálói alapvető kiszolgálóra vagy kiszolgálómagra nem támogatott.

* Az Azure AD Connect et windows Server 2012-es vagy újabb rendszerre kell telepíteni. A kiszolgálónak tartományhoz kell csatlakoznia, és lehet tartományvezérlő vagy tagkiszolgáló.
* Az Azure AD Connect-kiszolgáló nem rendelkezhet a PowerShell-átírási csoportházirenddel, ha az ADFS-konfiguráció kezeléséhez az Azure AD Connect varázslót használja. Engedélyezheti a PowerShell-átírást, ha az Azure AD Connect varázslót használja a szinkronizálási konfiguráció kezeléséhez.
* Active Directory összevonási szolgáltatások telepítése kor, azoknak a kiszolgálóknak, amelyeken az AD FS vagy a WebApplication Proxy telepítve van, Windows Server 2012 R2 vagy újabb kiszolgálónak kell lenniük. [A Windows távfelügyeletét](#windows-remote-management) engedélyezni kell ezeken a kiszolgálókon a távtelepítéshez.
* Ha az Active Directory összevonási szolgáltatások telepítése folyamatban van, [TLS/SSL-tanúsítványokra](#tlsssl-certificate-requirements)van szükség.
* Ha az Active Directory összevonási szolgáltatások telepítése folyamatban van, konfigurálnia kell a [névfeloldást.](#name-resolution-for-federation-servers)
* Ha a globális rendszergazdák engedélyezve vannak **https://secure.aadcdn.microsoftonline-p.com** az MFA-val, akkor az URL-címnek szerepelnie kell a megbízható helyek listájában. A rendszer kéri, hogy vegye fel ezt a webhelyet a megbízható helyek listájára, amikor a rendszer többszintű farendszer-kihívást kér, és azt korábban nem adta hozzá. Az Internet Explorer segítségével hozzáadhatja a megbízható helyekhez.
* A Microsoft azt javasolja, hogy az Azure AD Connect-kiszolgáló edzése csökkentse az informatikai környezet kritikus összetevőjének biztonsági támadási felületét.  Az alábbi javaslatok követése csökkenti a szervezetre vonatkozó biztonsági kockázatokat.

* Telepítse az Azure AD Connectet egy tartományhoz csatlakozó kiszolgálón, és korlátozza a rendszergazdai hozzáférést a tartományi rendszergazdákra vagy más szigorúan ellenőrzött biztonsági csoportokra.

További tudnivalókért lásd: 

* [Rendszergazdák csoportjainak védelme](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Beépített rendszergazdai fiókok védelme](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [A biztonság javítása és fenntartása a támadási felületek csökkentésével](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Az Active Directory támadási felületének csökkentése](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>Az Azure AD Connect által használt SQL-kiszolgáló
* Az identitásadatok tárolásához az Azure AD Connectnek szüksége van egy SQL Server-adatbázisra. Alapértelmezés szerint egy SQL Server 2012 Express LocalDB (az SQL Server Express egy könnyű verziója) van telepítve. Az SQL Server Express 10 GB-os méretkorláttal rendelkezik, amely körülbelül 100 000 objektum kezelését teszi lehetővé. Ha nagyobb mennyiségű címtárobjektumot kell kezelnie, a telepítővarázslót az SQL Server egy másik telepítésére kell irányítania. Az SQL Server telepítésének típusa hatással lehet az [Azure AD Connect teljesítményére.](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors)
* Ha az SQL Server egy másik telepítését használja, akkor a következő követelmények érvényesek:
  * Az Azure AD Connect támogatja a Microsoft SQL Server összes verzióját 2012-től (a legújabb Service Pack csomaggal) az SQL Server 2019-ig. A Microsoft Azure SQL Database **adatbázisként nem támogatott.**
  * A kis- és nagybetűk et nem megkülönböztető SQL-illesztést kell használnia. Ezeket a egyeztetéseket \_egy CI_ azonosítják a nevükben. Nem **támogatott** a kis- és nagybetűk megkülönböztetése, amelyet \_a CS_ a nevükben azonosítanak.
  * SQL-példányonként csak egy szinkronizálási motor lehet. Nem **támogatott** az SQL-példány okainak megosztása fim/MIM Sync, DirSync vagy Azure AD Sync használatával.

### <a name="accounts"></a>Fiókok
* Az Azure AD globális rendszergazdai fiókja az Azure AD-bérlőhöz, amelyhez integrálni szeretne. Ennek a fióknak **iskolai vagy szervezeti fióknak** kell lennie, és nem lehet **Microsoft-fiók.**
* Ha [expressz beállításokat](reference-connect-accounts-permissions.md#express-settings-installation) használ, vagy a DirSync szolgáltatásból frissít, akkor rendelkeznie kell egy vállalati rendszergazdai fiókkal a helyszíni Active Directoryhoz.
* Ha az egyéni beállítások telepítési útvonalát használja, akkor több lehetősége van. További információt az [Egyéni telepítési beállítások című](reference-connect-accounts-permissions.md#custom-installation-settings)témakörben talál.

### <a name="connectivity"></a>Kapcsolatok
* Az Azure AD Connect kiszolgálónak DNS-feloldásra van szüksége az intranethez és az internethez. A DNS-kiszolgálónak képesnek kell lennie a nevek feloldására mind a helyszíni Active Directory, mind az Azure AD-végpontok között.
* Ha az intraneten tűzfalak vannak, és meg kell nyitnia az Azure AD Connect-kiszolgálók és a tartományvezérlők közötti portokat, további információt az [Azure AD Connect Ports](reference-connect-ports.md) című témakörben talál.
* Ha a proxy vagy a tűzfal korlátozza, hogy mely URL-címek érhetők el, akkor meg kell nyitni az [Office 365 URL-címeiben és IP-címtartományaiban](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) dokumentált URL-címeket.
  * Ha németországban használja a Microsoft Cloud ot vagy a Microsoft Azure Government felhőt, tekintse meg az [Azure AD Connect szinkronizálási szolgáltatáspéldányainak](reference-connect-instances.md) URL-címekkel kapcsolatos szempontjait.
* Az Azure AD Connect (1.1.614.0-s és az azt követő verzió) alapértelmezés szerint a TLS 1.2-t használja a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához. Ha a TLS 1.2 nem érhető el az alapul szolgáló operációs rendszeren, az Azure AD Connect növekményesen visszaáll a régebbi protokollokra (TLS 1.1 és TLS 1.0).
* Az 1.1.614.0-s verzió előtt az Azure AD Connect alapértelmezés szerint a TLS 1.0-s verzióját használja a szinkronizálási motor és az Azure AD közötti kommunikáció titkosításához. A TLS 1.2-es szintre való váltáshoz kövesse a [TLS 1.2 engedélyezése](#enable-tls-12-for-azure-ad-connect)az Azure AD Connect hez című részben leírt lépéseket.
* Ha kimenő proxyt használ az internethez való csatlakozáshoz, a következő beállítást a **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** fájl hozzáadásával kell hozzáadni a telepítővarázslóhoz, és az Azure AD Connect szinkronizálása az internethez és az Azure AD-hez való csatlakozáshoz. Ezt a szöveget a fájl alján kell beírni. Ebben a &lt;kódban&gt; a PROXYADDRESS a tényleges proxy IP-címét vagy állomásnevét jelöli.

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

* Ha a proxykiszolgáló hitelesítést igényel, akkor a [szolgáltatásfióknak](reference-connect-accounts-permissions.md#adsync-service-account) a tartományban kell lennie, és a testreszabott beállítások telepítési elérési útját kell használnia az [egyéni szolgáltatásfiók](how-to-connect-install-custom.md#install-required-components)megadásához. A machine.config fájlmódosítása is eltérő. Ezzel a machine.config fájllal a telepítővarázsló és a szinkronizálási motor válaszol a proxykiszolgáló hitelesítési kérelmeire. A telepítővarázsló összes lapján , a **Konfigurálás** lap kivételével, a rendszer a bejelentkezett felhasználó hitelesítő adatait használja. A telepítővarázsló végén található **Konfigurálás** lapon a környezet az Ön által létrehozott [szolgáltatásfiókra](reference-connect-accounts-permissions.md#adsync-service-account) vált. A machine.config szakasz így kell kinéznie.

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

* Amikor az Azure AD Connect webkérelmet küld az Azure AD-nek a címtár-szinkronizálás részeként, az Azure AD-nek akár 5 perc ig is eltarthat a válaszadás. Gyakori, hogy a proxykiszolgálók kapcsolat tétlen időtúllépési konfigurációval rendelkeznek. Ellenőrizze, hogy a konfiguráció legalább 6 percre van-e beállítva.

További információt az MSDN az [alapértelmezett proxyelemről című témakörben talál.](https://msdn.microsoft.com/library/kd3cf2ex.aspx)  
Ha problémák merülnek fel a kapcsolattal kapcsolati problémákról, olvassa el a [Kapcsolódási problémák elhárítása című témakört.](tshoot-connect-connectivity.md)

### <a name="other"></a>Egyéb
* Nem kötelező: A szinkronizálás ellenőrzésére szolgáló teszt felhasználói fiók.

## <a name="component-prerequisites"></a>Összetevő előfeltételei
### <a name="powershell-and-net-framework"></a>PowerShell és a .
Az Azure AD Connect a Microsoft PowerShelltől és a .NET Framework 4.5.1-es verziótól függ. Ezt a vagy egy újabb verziót kell telepíteni a kiszolgálóra. A Windows Server verziójától függően tegye a következőket:

* Windows Server 2012R2
  * A Microsoft PowerShell alapértelmezés szerint telepítve van. Semmit nem kell tenni.
  * A . Ellenőrizze, hogy telepítette-e a Windows Server legújabb frissítéseit a Vezérlőpulton.
* Windows Server 2012
  * A Microsoft PowerShell legújabb verziója a **Windows Management Framework 4.0**rendszerben érhető el, amely elérhető a [Microsoft letöltőközpontban.](https://www.microsoft.com/downloads)
  * A . [Microsoft Download Center](https://www.microsoft.com/downloads)


### <a name="enable-tls-12-for-azure-ad-connect"></a>TLS 1.2 engedélyezése az Azure AD Connecthez
Az 1.1.614.0-s verzió előtt az Azure AD Connect alapértelmezés szerint a TLS 1.0-s verzióját használja a szinkronizálási motorkiszolgáló és az Azure AD közötti kommunikáció titkosításához. Ezt úgy módosíthatja, hogy a .NET alkalmazásokat úgy állítja be, hogy alapértelmezés szerint a TLS 1.2-t használják a kiszolgálón. A TLS 1.2-ről további információt a [Microsoft 2960358 számú biztonsági tanácsadójában talál.](https://technet.microsoft.com/security/advisory/2960358)

1.  Győződjön meg arról, hogy a .NET 4.5.1 gyorsjavítás telepítve van az operációs rendszerhez, olvassa el a [Microsoft 2960358 biztonsági tanácsadóját.](https://technet.microsoft.com/security/advisory/2960358) Lehet, hogy ez a gyorsjavítás vagy egy későbbi kiadás már telepítve van a kiszolgálón.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "SchUseStrongCrypto"=dword:00000001
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
