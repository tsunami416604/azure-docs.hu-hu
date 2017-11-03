---
title: "Az Azure verem datacenter integrációs - DNS"
description: "Útmutató: Azure verem DNS integrálása a DNS adatközpontot"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/10/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: 40d6d4858ef2e3df61d04dc68c00e09c04f000e2
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---dns"></a>Az Azure verem datacenter integrációs - DNS

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Az Azure verem végpontok hozzáférhetnek (`portal`, `adminportal`, `management`, `adminmanagement`stb.)  Azure külső-veremből a DNS-zónák Azure verem használni kívánt futtató DNS-kiszolgálók az Azure verem DNS-szolgáltatások integrálni kell.

## <a name="azure-stack-dns-namespace"></a>Az Azure verem DNS-névtér
Szükségesek bizonyos Azure verem telepítésekor DNS kapcsolatos fontos információkat tartalmaznak.


|Mező  |Leírás  |Példa|
|---------|---------|---------|
|Régió|Az Azure Alkalmazásveremben üzembe földrajzi helyét.|`east`|
|Külső tartomány neve|Az Azure-verem központi telepítéshez használni kívánt a zóna nevét.|`cloud.fabrikam.com`|
|Belső tartománynév|Az infrastruktúra-szolgáltatásokat Azure-készletben használt belső zóna nevét.  Címtárszolgáltatás integrált és titkos (nem érhető el a az Azure Alkalmazásveremben üzembe kívül).|`azurestack.local`|
|DNS-továbbító|DNS-kiszolgálók, amelyek segítségével az DNS-lekérdezéseket, a DNS-zónák és az azt jelzi, hogy az Azure-vermet, a vállalati intranet vagy a nyilvános interneten kívül üzemeltetett.|`10.57.175.34`<br>`8.8.8.8`|
|Névelőtagjának (nem kötelező)|Az elnevezési előtag azt szeretné, hogy az Azure-verem infrastruktúra példány gép szerepkörneveket kell rendelkeznie.  Ha nincs megadva, az alapértelmezett érték `azs`.|`azs`|

A teljesen minősített tartományneve (FQDN) az Azure Alkalmazásveremben üzembe és a végpontok a régió és a külső tartománynév paraméter kombinációjából áll. A példákban szereplő értékek felhasználásával az előző táblázatban, lenne az FQDN a Azure verem központi telepítés a következő néven:

`east.cloud.fabrikam.com`

Példák a végpontok a központi telepítés olyan, a következő URL-címek jelenne meg:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

A Példa DNS névtér használatához Azure verem központi telepítésre, az alábbi feltételek szükségesek:

- A zóna `fabrikam.com` regisztrálva van-e a tartományregisztráló, egy belső vállalati DNS-kiszolgálót vagy mindkettőt, attól függően, hogy a megoldás vonatkozó követelményeknek.
- A gyermektartomány `cloud.fabrikam.com` létezik-e a zóna `fabrikam.com`.
- A DNS-kiszolgálók, amelyek a zónákat `fabrikam.com` és `cloud.fabrikam.com` elérhető az Azure-verem telepítéséből.

Kell tudni oldania az Azure-verem végpontok és példányok külső Azure veremből DNS-neveit, a DNS-kiszolgálók, amelyek a külső DNS-zóna üzemeltetésére Azure verem a használni kívánt szülőzónát futtató DNS-kiszolgálók integrálni kell.


## <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

- Egy mérvadó DNS-kiszolgáló DNS-zónákat üzemeltet. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
- A rekurzív DNS-kiszolgáló nem üzemeltet DNS-zónákat. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

Azure verem tartalmazza a mérvadó is és a rekurzív DNS-kiszolgálók. A rekurzív kiszolgálók névfeloldását minden, a kivételével a belső személyes és a külső nyilvános DNS-zónát, hogy Azure Alkalmazásveremben üzembe szolgálnak. 

![Az Azure DNS verem architektúrája](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Azure-veremből külső DNS-nevek feloldása

A DNS-nevek végpontok Azure verem kívül (például: www.bing.com), meg kell adnia a DNS-kiszolgálók, amelyek használatával Azure verem amelyek Azure verem esetében nem mérvadó DNS-kérelmek továbbítása. Üzembe helyezés esetén a DNS-kiszolgálók, amely Azure verem kérelmeket továbbítja a központi telepítési munkalapra (a DNS-továbbító mezőben) kell megadni. Adjon meg legalább két kiszolgáló ebben a mezőben a hibatűrés. Ezek az értékek nélkül Azure verem telepítése sikertelen.

### <a name="configure-conditional-dns-forwarding"></a>Feltételes DNS-továbbító konfigurálása

> [!IMPORTANT]
> Ez csak egy AD FS üzembe helyezése vonatkozik.

Ahhoz, hogy a névfeloldás a meglévő DNS-infrastruktúrával, konfigurálja a feltételes továbbítást.

A feltételes továbbítók hozzáadásához a kiemelt végpont kell használnia.

Ez az eljárás az adatközponti hálózat, amely képes kommunikálni a kiemelt végpont Azure-készletben lévő számítógépet használni.

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), és kapcsolódjon a kiemelt végpont IP-címét. A hitelesítő adatok használata CloudAdmin hitelesítéshez.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. A privilegizált végponthoz való kapcsolódás után futtassa a következő PowerShell-parancsot. Helyettesítse be a tartomány nevét a megadott minta értékek és a DNS-kiszolgálók IP-címeket szeretne használni.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Külső Azure veremből Azure verem DNS-nevek feloldása
A mérvadó kiszolgálók esetén a gazdarendszerhez a külső DNS-zóna adatainak tárolásához, és a felhasználó által létrehozott zónák. Ezek a kiszolgálók engedélyezéséhez a zónadelegálás vagy feltételes továbbítás külső Azure veremből Azure verem DNS-nevek feloldására integrálható.

## <a name="get-dns-server-external-endpoint-information"></a>DNS-kiszolgáló külső végpont információk

A DNS-infrastruktúra az Azure Alkalmazásveremben üzembe integrálása, szüksége van a következő információkat:

- DNS-kiszolgáló teljes tartománynevek
- DNS-kiszolgáló IP-címek

Az Azure verem DNS-kiszolgálók teljes tartományneveit formátuma a következő:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

A minta értékek, a teljes tartományneveket, a DNS-kiszolgálók vannak:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Ezt az információt is létrejön, minden Azure verem telepítések nevű fájlba végén `AzureStackStampDeploymentInfo.json`. Ez a fájl megtalálható a `C:\CloudDeployment\logs` mappában található a központi telepítés virtuális gépen. Ha nem biztos abban, hogy milyen értékeket az Azure-verem üzembe helyezéshez használt, az értékek lekérheti itt.

Ha a központi telepítés virtuális gép már nem érhető el, vagy nem érhető el, szerezheti be az értékeket a kiemelt végpont csatlakozik, és fut a `Get-AzureStackInfo` PowerShell-parancsmagot. A privilegizált végpont kapcsolatos további információkért lásd: (Beszúrás csatolása a következő cikket: Itt).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Feltételes továbbítás Azure verem beállítása

A legegyszerűbb és legbiztonságosabb Azure verem integrálása a DNS-infrastruktúra például úgy a zóna feltételes továbbítás a szülőzónát futtató kiszolgálótól. Ezt a módszert akkor ajánlott, ha rendelkezik a DNS-kiszolgálók közvetlenül lehet szabályozni a szülőzónát futtató a Azure verem külső DNS-névtérben.

Ha még nem ismeri a DNS-beli feltételes továbbítás módjáról, tekintse meg a következő TechNet cikkben találhat: [rendelje hozzá a feltételes továbbítók egy tartománynév](https://technet.microsoft.com/library/cc794735), vagy a DNS-megoldást adott dokumentációjában.

Olyan esetekben, ahol a külső Azure verem DNS-zóna hasonló vállalati tartománynév tartomány megadott feltételes továbbítás nem használható. DNS-delegálást konfigurálni kell.

Példa:

- Vállalati DNS-tartománynév:`contoso.com`
- Az Azure verem külső DNS-tartománynév:`azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>A külső DNS-zónáját az Azure-verem delegálása

Egy Azure Alkalmazásveremben üzembe kívül feloldható DNS-nevek, a DNS-delegálás beállítása kell.

Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. A regisztráló DNS kezelése lapon szerkessze a Névkiszolgálói rekordokat, és cserélje le a Névkiszolgálói rekordokat, a zóna Azure verem megfelelően.

A legtöbb DNS-regisztráló szervezetek esetén meg kell adni legalább két DNS-kiszolgáló a delegálás befejezéséhez.

## <a name="next-steps"></a>Következő lépések

[Az Azure verem datacenter integrációs - identitás](azure-stack-integrate-identity.md)
