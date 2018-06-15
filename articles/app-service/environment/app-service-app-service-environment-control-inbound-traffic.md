---
title: Az App Service-környezetek bejövő forgalom vezérlése
description: További tudnivalók az App Service-környezetek bejövő forgalmat hálózati biztonsági szabályok konfigurálása.
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.openlocfilehash: ed72bf3202d6cb2d2161bc0df693d3e6a1fc58ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836923"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Az App Service-környezetek bejövő forgalom vezérlése
## <a name="overview"></a>Áttekintés
Az App Service-környezetek hozhatók létre **vagy** az Azure Resource Manager virtuális hálózati **vagy** klasszikus telepítési modell [virtuális hálózati][virtualnetwork].  Egy új virtuális hálózat és az új alhálózat egy App Service Environment-környezet létrehozásakor adhatók meg.  Másik lehetőségként az App Service-környezetek is létrehozható egy már meglévő virtuális hálózat és a korábban létrehozott alhálózati.  2016. június a módosítások a ASEs is is telepíthető az nyilvános címtartományt, vagy RFC1918 címterek (azaz Magáncímeket) használó virtuális hálózatok.  További információ az App Service-környezetek létrehozásáról: [egy App Service Environment-környezet létrehozása][HowToCreateAnAppServiceEnvironment].

Egy App Service Environment-környezet mindig kell létrehozni egy alhálózaton belül, mert alhálózat biztosít egy olyan hálózathatárhoz zárolását bejövő forgalom mögött fölérendelt eszközöket és szolgáltatásokat úgy, hogy a HTTP és HTTPS-forgalom felsőbb rétegbeli IP-címek csak elfogadható használható.

Bejövő és kimenő hálózati forgalom az alhálózat segítségével kezelhető egy [hálózati biztonsági csoport][NetworkSecurityGroups]. Bejövő forgalom vezérlése hoz létre a hálózati biztonsági szabályok a hálózati biztonsági csoport, és ezután a hálózati biztonsági csoport az App Service-környezet tartalmazó alhálózat szükséges.

Ha hálózati biztonsági csoport alhálózathoz van hozzárendelve, az App Service Environment-környezet az alkalmazások bejövő forgalmát engedélyezett/zárolva van alapján az engedélyezési és megtagadási szabályoknak a hálózati biztonsági csoport meghatározott.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Szerepel az App Service-környezetek bejövő hálózati portok
A hálózati biztonsági csoportok bejövő hálózati forgalom zárolása, mielőtt fontos tudni, hogy a szükséges és választható hálózati portok – az App Service-környezetek készletét.  Bizonyos portok felé irányuló forgalom ki véletlenül bezárja azt eredményezheti, hogy működésképtelenné egy App Service Environment-környezetben.

Az App Service Environment-környezet által használt portok listája a következő: Minden portjait **TCP**, ha nincs másként jelezve egyértelműen:

* 454: **port szükséges** kezelése és karbantartása az App Service Environment-környezetek SSL-en keresztüli Azure-infrastruktúra használják.  Blokkolja ezt a portot irányuló forgalmat.  Ez a port mindig egy hajlamosnak nyilvános VIP van kötve.
* 455: **port szükséges** kezelése és karbantartása az App Service Environment-környezetek SSL-en keresztüli Azure-infrastruktúra használják.  Blokkolja ezt a portot irányuló forgalmat.  Ez a port mindig egy hajlamosnak nyilvános VIP van kötve.
* 80: alapértelmezett port a bejövő HTTP-forgalmat az App Service-csomag egy App Service Environment-környezetben futó alkalmazások számára.  A ILB-kompatibilis ASE ezt a portot a ASE ILB címét van kötve.
* 443-as: alapértelmezett port a bejövő SSL forgalmát az App Service-csomag egy App Service Environment-környezetben futó alkalmazások.  A ILB-kompatibilis ASE ezt a portot a ASE ILB címét van kötve.
* 21: FTP-vezérlőcsatorna.  Ez a port biztonságosan blokkolható, ha az FTP nem használja.  Egy ILB-kompatibilis ASE a ezt a portot is köthető ILB címre egy ASE a.
* 990: ftps-t a vezérlőcsatornához.  Ez a port biztonságosan blokkolható, ha még nem használ ftps-t.  Egy ILB-kompatibilis ASE a ezt a portot is köthető ILB címre egy ASE a.
* 10001-10020: FTP-adatcsatornákhoz.  Csakúgy, mint a vezérlőcsatorna, ezeket a portokat biztonságosan blokkolható Ha FTP nincs használatban.  A ILB-kompatibilis ASE ezt a portot a ASE ILB cím köthető.
* 4016: a Visual Studio 2012 távoli hibakereséshez.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nem használja.  A ILB-kompatibilis ASE ezt a portot a ASE ILB címét van kötve.
* 4018: a Visual Studio 2013 távoli hibakereséshez.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nem használja.  A ILB-kompatibilis ASE ezt a portot a ASE ILB címét van kötve.
* 4020: a Visual Studio 2015-höz távoli hibakereséshez.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nem használja.  A ILB-kompatibilis ASE ezt a portot a ASE ILB címét van kötve.

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolatok és a DNS-követelmények
Egy App Service Environment-környezet megfelelő működéséhez ez is igényli a különböző végpontok kimenő hozzáférést. "A hálózati kapcsolat szükséges" szakaszában van a külső végpontok egy ASE által használt teljes listáját a [ExpressRoute tartozó fürthálózat-konfiguráció](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) cikk.

App Service-környezetek virtuális hálózat egy érvényes DNS-infrastruktúra szükséges.  Ha bármilyen okból az App Service Environment-környezet létrehozása után a DNS-konfiguráció módosul, a fejlesztők kényszerítheti az App Service-környezet az új DNS-konfiguráció érvényesítéséhez.  Az az App Service Environment-felügyelet panel tetején található indítására, a "Restart" ikonnal működés közbeni környezet újraindítás a [Azure-portálon] [ NewPortal] okoz a környezetben, az új DNS-konfiguráció érvényesítéséhez.

Ajánlott továbbá, hogy a virtuális hálózaton egyéni DNS-kiszolgálók kell-e a telepítő az App Service Environment-környezet létrehozása előtt időben.  Ha egy virtuális hálózat DNS-konfiguráció megváltozott egy App Service Environment-környezet létrehozása közben, az App Service Environment-környezet létrehozása folyamat sikertelen vezethet.  Egy hasonló tekintettel az egy egyéni DNS-kiszolgáló létezik a VPN-átjáró másik végén, és a DNS-kiszolgáló nem érhető el vagy nem érhető el, ha az App Service Environment-környezet létrehozása is sikertelen lesz.

## <a name="creating-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Teljes hogyan hálózati biztonsági csoportok témakör a következő [információk][NetworkSecurityGroups].  Az Azure Service Management az alábbi példa koppint, a hálózati biztonsági csoportok konfigurálása és alkalmazása a hálózati biztonsági csoport, amely tartalmazza az App Service-környezetek alhálózathoz egy aktuális mutatja be.

**Megjegyzés:** hálózati biztonsági csoportok grafikusan használatával konfigurálható a [Azure Portal](https://portal.azure.com) vagy Azure PowerShell használatával.

Hálózati biztonsági csoportok először egy előfizetéshez tartozó önálló egységként jönnek létre. Mivel a hálózati biztonsági csoportok Azure-régióban jönnek létre, győződjön meg arról, hogy a hálózati biztonsági csoport jön létre az App Service Environment-környezet ugyanabban a régióban.

A következő mutatja be, a hálózati biztonsági csoport létrehozása:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Hálózati biztonsági csoport létrehozása után egy vagy több hálózati biztonsági szabályok hozzáadása történik meg azt.  Mivel a szabályok idővel megváltozhat, javasoljuk, hogy tér el a további szabályok beszúrása időbeli megkönnyítése szabályának prioritásait használt számozási séma.

Az alábbi példában látható egy szabályt, amely explicit módon engedélyezi a hozzáférést a kezeli és tartja karban az App Service Environment-környezet az Azure infrastruktúra által igényelt felügyeleti portokat.  Ne feledje, hogy az összes felügyeleti adatforgalmat SSL-en keresztül zajló kommunikációról védi-e ügyféltanúsítványokat, ezért annak ellenére, hogy a portokat az Azure felügyeleti infrastruktúra eltérő bármely entitás nem érhető el.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Zárolása port a 80-as és 443-as "elrejtőzni" egy App Service-környezet mögött felsőbb rétegbeli eszközök és szolgáltatások elérését, amikor Ön tudniuk kell, hogy a felsőbb rétegbeli IP-címet.  Például ha webalkalmazási tűzfal (WAF) használ, a WAF lesz a saját IP-cím (vagy címek) használó, ha egy alsóbb rétegbeli App Service Environment-környezet proxyhasználat forgalmat.  Ez az IP-cím használatára szüksége lesz a *SourceAddressPrefix* paraméter egy hálózati biztonsági szabály.

Az alábbi példában egy adott felsőbb rétegbeli IP-címről érkező bejövő forgalom kifejezetten engedélyezett.  A cím *1.2.3.4* helyőrzőként szolgál egy fölérendelt WAF IP-címét.  Módosítsa az értéket a felsőbb rétegbeli eszköz vagy a szolgáltatás által használt cím kereséséhez.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

FTP-támogatás van szükség, ha a következő szabályok segítségével sablonként adjon hozzáférést az FTP-vezérlő port és az adatok portok.  Mivel FTP állapot-nyilvántartó protokoll, nem lehet képes továbbítani az FTP-forgalmat egy hagyományos HTTP/HTTPS tűzfalhoz vagy proxyhoz eszközön keresztül.  Ebben az esetben szüksége lesz beállítva a *SourceAddressPrefix* más értékre – például a IP-címtartomány fejlesztői vagy a központi telepítési gépek mely FTP ügyfelek futnak. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Megjegyzés:** az adatcsatorna porttartománya módosíthatja a próbaidőszak alatt.)

Távoli hibakeresés a Visual Studio használata esetén a következő szabályok bemutatják, hogyan engedélyezheti a hozzáférést.  Van külön szabály minden Visual Studio támogatott verziója óta egyes verzióihoz távoli hibakereséshez másik portot használ.  FTP-hozzáférést, a távoli hibakeresési forgalom előfordulhat, hogy nem megfelelő sorrendben egy hagyományos WAF vagy a proxy-eszközön keresztül.  A *SourceAddressPrefix* helyett az IP-címtartomány futó Visual Studio developer gépek állítható be.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Hálózati biztonsági csoport hozzárendelése egy alhálózaton
Hálózati biztonsági csoport tartozik egy alapértelmezett biztonsági szabály, amely megtagadja a hozzáférést az összes külső forgalom.  A fent leírt hálózati biztonsági szabályok és a bejövő forgalom blokkolása alapértelmezett szabály az eredménye, hogy csak a társított címtartományokból forrás felé irányuló forgalmat egy *engedélyezése* művelet fog tudni forgalmat küldeni egy App Service Environment-környezetben futó alkalmazások.

Miután elkészült a hálózati biztonsági csoport biztonsági szabályait, kell az App Service-környezet tartalmazó alhálózat hozzárendelését.  A hozzárendelési utasítás hivatkozik, mind az App Service Environment-környezet tartalmazó a virtuális hálózat nevét, valamint az alhálózat, amelyen létrehozták az App Service Environment-környezet nevét.  

Az alábbi példában látható, a hálózati biztonsági csoport folyik a hozzárendelése olyan alhálózat és virtuális hálózathoz:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Ha a hálózati biztonsági csoport a helyhozzárendelés képes lesz (a hozzárendelés egy hosszú futású műveleteket és néhány percet is igénybe vehet.), csak a forgalom megfelelő bejövő *engedélyezése* szabályok sikeresen eléri a alkalmazásokat az App Service Environment-környezetben.

A teljesség a következő példa bemutatja, hogyan távolítsa el, és így dis-rendelje hozzá a hálózati biztonsági csoport az alhálózatból:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Explicit IP-SSL szempontjai
Ha egy alkalmazás explicit IP-SSL-címmel van konfigurálva (alkalmazható *csak* való, amelyek egy nyilvános VIP ASEs), az App Service Environment-környezet alapértelmezett IP-címe helyett a HTTP és HTTPS forgalmat, folytatódjon az alhálózat egy másik készleten a 80-as és 443-as portot eltérő portok.

Az egyes kulcspár minden IP-SSL cím által használt portok a portál felhasználói felületen az App Service Environment részletek UX paneljén található.  Jelölje be "az összes beállítások"--> "IP-címek".  Az "IP-címek" panel összes explicit módon konfigurált IP-SSL címek táblázatát jeleníti meg, az App Service Environment-környezet, együtt a speciális port pár, amellyel minden IP-SSL címhez kapcsolódó HTTP és HTTPS-forgalmat.  A port párt a DestinationPortRange paraméterek kell használni, amikor a szabályok konfigurálása a hálózati biztonsági csoport.

Ha egy alkalmazást egy ASE az IP-SSL használatára van konfigurálva, a külső ügyfelek nem jelenik meg, és nem kell aggódnia a különleges pár porthozzárendelést.  Az alkalmazás felé irányuló forgalom általában erdőtől áramolnak a konfigurált IP-SSL címet.  A fordítás a speciális port párhoz automatikusan történik, belső adatforgalom utolsó szakasza során a ASE tartalmazó alhálózat be. 

## <a name="getting-started"></a>Bevezetés
App Service Environment-környezetek megkezdéséhez, lásd: [App Service Environment bemutatása][IntroToAppServiceEnvironment]

További részletek az App Service-környezetek háttér erőforrás biztonságosan kapcsolódó alkalmazásokra körül: [biztonságosan való csatlakozás háttér erőforrások az App Service-környezetek][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

