---
title: App Service Environment - Azure bejövő forgalom szabályozása
description: Ismerje meg az App Service-környezet bejövő forgalom szabályozása a hálózati biztonsági szabályok konfigurálása.
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
ms.custom: seodec18
ms.openlocfilehash: 84575dcb67845a074ce19cf9d819e1dda3f90e20
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271974"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Az App Service-környezet bejövő forgalom szabályozása
## <a name="overview"></a>Áttekintés
Az App Service-környezet hozható létre **vagy** egy Azure Resource Managerbeli virtuális hálózat **vagy** klasszikus telepítési modell [virtuális hálózat] [ virtualnetwork].  App Service-környezet létrehozásakor egy új virtuális hálózat és egy új alhálózatot kell meghatározni.  Azt is megteheti App Service-környezet is létrehozható egy már meglévő virtuális hálózat és a meglévő alhálózat.  2016. június egyik módosítását az ASE is üzembe helyezhetők a címtartomány nyilvános, vagy az RFC1918 címterek (azaz a magánhálózati címek) használó virtuális hálózatokra.  További részleteket az App Service-környezet létrehozása [App Service-környezet létrehozása][HowToCreateAnAppServiceEnvironment].

App Service-környezet mindig belül kell létrehoznia egy alhálózathoz, mert az alhálózat biztosít egy hálózathatárt, amely zárolhat rétegbeli eszközök és szolgáltatások mögötti bejövő forgalmat, hogy az adott csak elfogadja a HTTP és HTTPS-forgalom használható felsőbb rétegbeli IP-címeket.

Bejövő és kimenő hálózati forgalmat egy alhálózaton található szabályzatokkal vezérelhető a [hálózati biztonsági csoport][NetworkSecurityGroups]. Hálózati biztonsági szabályok létrehozása egy hálózati biztonsági csoportot, és majd hozzárendelése a hálózati biztonsági csoportot az alhálózatot, amely tartalmazza az App Service Environment bejövő forgalom szabályozása igényel.

Hálózati biztonsági csoport hozzárendelése egy alhálózathoz, után az App Service-környezet az alkalmazások bejövő forgalmának engedélyezett/zárolva van alapján az engedélyezési és megtagadási szabályoknak a hálózati biztonsági csoport meghatározott.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>App Service Environment-környezetben használt bejövő hálózati portok
A hálózati biztonsági csoport bejövő hálózati forgalom meghatározott sémákra kelljen, mielőtt fontos tudni, hogy az App Service-környezet által használt szükséges és választható hálózati portok készletét.  Bizonyos portok forgalom ki véletlenül bezárja azt eredményezheti, hogy működésképtelenné App Service Environment-környezetben.

App Service-környezet által használt portok listáját a következő: Minden port **TCP**, ha másként nincs jelezve egyértelműen:

* 454:  **Szükséges port** kezelésével és karbantartásával App Service Environment-környezetek SSL-en keresztül az Azure-infrastruktúra által használt.  Nem blokkolja a forgalmat ezen a porton.  Ezt a portot a nyilvános virtuális IP-cím egy ASE környezethez mindig van kötve.
* 455:  **Szükséges port** kezelésével és karbantartásával App Service Environment-környezetek SSL-en keresztül az Azure-infrastruktúra által használt.  Nem blokkolja a forgalmat ezen a porton.  Ezt a portot a nyilvános virtuális IP-cím egy ASE környezethez mindig van kötve.
* 80:  Alapértelmezett port a bejövő HTTP-forgalom App Service-csomagban App Service Environment-környezetben futó alkalmazásokra.  Ez a port az ASE ILB-kompatibilis, az ASE ILB-címmel van kötve.
* 443: Alapértelmezett port bejövő SSL-forgalmat az App Service-csomagok App Service Environment-környezetben futó alkalmazásokra.  Ez a port az ASE ILB-kompatibilis, az ASE ILB-címmel van kötve.
* 21:  Az FTP-Kiszolgálójának vezérlőcsatorna.  Ez a port biztonságosan blokkolható, ha az FTP nem használja.  Az ASE ILB-kompatibilis ezt a portot is köthető az ILB-esetében egy ASE Környezethez.
* 990:  FTPS vezérlőcsatorna.  Ez a port biztonságosan blokkolható, ha FTPS nincs használatban.  Az ASE ILB-kompatibilis ezt a portot is köthető az ILB-esetében egy ASE Környezethez.
* 10001-10020: FTP-adatcsatornák.  Csakúgy, mint a vezérlőcsatorna, ezeket a portokat biztonságosan blokkolható, ha az FTP nem használja.  Az ASE ILB-kompatibilis ezt a portot is köthető az ASE ILB-címmel.
* 4016: Távoli hibakeresés a Visual Studio 2012 használja.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nem használja.  Ez a port az ASE ILB-kompatibilis, az ASE ILB-címmel van kötve.
* 4018: Távoli hibakeresés a Visual Studio 2013 használja.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nem használja.  Ez a port az ASE ILB-kompatibilis, az ASE ILB-címmel van kötve.
* 4020: Távoli hibakeresés a Visual Studio 2015-öt használja.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nem használja.  Ez a port az ASE ILB-kompatibilis, az ASE ILB-címmel van kötve.

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolat és DNS-követelmények
Az App Service-környezet megfelelő működéséhez azt is megköveteli a különféle végpontok a kimenő hozzáférést. Az "A hálózati kapcsolat szükséges" szakaszában szerepel a külső végpontokat az ASE által használt teljes listáját a [az ExpressRoute hálózati konfiguráció](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) cikk.

App Service Environment-környezetek egy érvényes DNS-infrastruktúra a virtuális hálózat szükséges.  Ha bármilyen okból a DNS-konfigurációt egy App Service Environment-környezet létrehozása után módosul, a fejlesztők kényszerítheti egy App Service Environment folytattuk a munkát az új DNS-konfiguráció.  Az az App Service Environment-felügyelet panel tetején található "Restart" ikonra működés közbeni környezet újraindítás elindítása a [az Azure portal] [ NewPortal] okoz az új DNS-t választja ki a környezet konfiguráció.

Emellett javasoljuk, hogy minden egyéni DNS-kiszolgáló a virtuális hálózaton kell-e időben App Service-környezet létrehozása előtt a telepítő.  Ha egy virtuális hálózat DNS-konfiguráció módosul, míg az App Service-környezet létrehozása folyamatban van, az App Service Environment-környezet létrehozása folyamat sikertelen, amely fog eredményezni.  Egy hasonló tekintettel az egyéni DNS-kiszolgáló létezik VPN gateway másik végén, és a DNS-kiszolgáló nem érhető el vagy nem érhető el, ha az App Service Environment-környezet létrehozása is sikertelen lesz.

## <a name="creating-a-network-security-group"></a>A hálózati biztonsági csoport létrehozása
Milyen a hálózati biztonsági csoportok munkahelyi kapcsolatos részletes lásd a következő [információk][NetworkSecurityGroups].  Az Azure Service Management az alábbi példa éri el a hálózati biztonsági csoportok konfigurálása és alkalmazása egy hálózati biztonsági csoportot, amely tartalmazza az App Service Environment-alhálózathoz összpontosítva emeli ki.

**Megjegyzés:** Hálózati biztonsági csoportok grafikusan használatával konfigurálható a [az Azure Portal](https://portal.azure.com) vagy az Azure Powershellen keresztül.

Hálózati biztonsági csoportok jönnek létre az egy előfizetéshez tartozó önálló egységként először. Mivel az Azure-régióban a hálózati biztonsági csoportok jönnek létre, győződjön meg arról, hogy a hálózati biztonsági csoport jön létre az App Service-környezet ugyanabban a régióban.

A következő mutat be egy hálózati biztonsági csoport létrehozása:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

A hálózati biztonsági csoport létrehozása után egy vagy több hálózati biztonsági szabályok hozzáadja.  Mivel a szabálykészletet idővel változhat, ajánlott a lemezterület-szabályának prioritásait, hogy idővel további szabályok beszúrni használt számozási sémát ki.

Az alábbi példa bemutatja egy szabályt, amely kifejezetten hozzáférést biztosít a felügyeleti portokat az Azure-infrastruktúra kezelésére és karbantartására az App Service-környezet számára szükséges.  Vegye figyelembe, hogy az összes felügyeleti forgalom SSL-kapcsolaton keresztül zajlik, és védi-e ügyféltanúsítványokat, ezért annak ellenére, hogy a portokat, eltérő Azure kezelési infrastruktúra minden olyan entitás elérhetetlenné válnak.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Meghatározott sémákra kelljen port a 80-as és 443-as "elrejtőzni" App Service-környezet mögött felsőbb rétegbeli eszközök és szolgáltatások elérését, amikor kell ismernie a felsőbb rétegbeli IP-cím.  Például ha egy webalkalmazási tűzfal (WAF) használ, a WAF lesz a saját IP-cím (vagy címek) használó, amikor proxy-forgalom egy alsóbb rétegbeli App Service Environment-környezet.  Ez az IP-cím használatához szüksége lesz a *SourceAddressPrefix* paraméter egy hálózati biztonsági szabály.

Az alábbi példában egy adott felsőbb rétegbeli IP-címre érkező bejövő forgalmat külön nem engedélyezett.  A cím *1.2.3.4* helyettesíti szolgál egy felsőbb rétegbeli WAF IP-címét.  Módosítsa az értéket a felsőbb rétegbeli eszközök vagy a szolgáltatás által használt cím egyezik.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

FTP-támogatás van szükség, ha a következő szabályok segítségével sablonként az FTP-vezérlő port és az adatokhoz való hozzáférés biztosítása a channel-porttal.  Mivel az FTP állapot-nyilvántartó protokoll, nem lehet tudni FTP-forgalmat útvonal egy hagyományos HTTP/HTTPS tűzfal vagy proxy eszközön keresztül.  Ebben az esetben be kell állítania a *SourceAddressPrefix* más értékre – például az IP-címtartomány fejlesztői és a központi telepítési gépek, amelyek FTP ügyfelek futtatja. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Megjegyzés:** az adatcsatorna porttartománya az előzetes verzió ideje alatt változhat.)

Távoli hibakeresés a Visual Studio használata esetén a következő szabályok bemutatják, hogyan lehet hozzáférést biztosítani.  Mivel minden verzió egy másik portot használ a távoli hibakeresés hajtható végre, nincs külön szabály minden támogatott verziót, a Visual Studio.  FTP-hozzáférést, a távoli hibakeresési előfordulhat, hogy nem adatforgalom megfelelően egy hagyományos WAF vagy a proxy eszközön keresztül.  A *SourceAddressPrefix* ehelyett beállítható az IP-címtartományt futó Visual Studio fejlesztői gépek.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>A hálózati biztonsági csoport hozzárendelése egy alhálózathoz
Hálózati biztonsági csoport tartozik egy alapértelmezett biztonsági szabály, amely megtagadja a hozzáférést minden külső forgalom.  A hálózati biztonsági szabályok a fent leírt, és az alapértelmezett biztonsági szabály blokkolja a bejövő forgalom az eredménye a társított forrás címtartományok csak forgalmát egy *engedélyezése* művelet lesz küldhet App Service Environment-környezetben futó alkalmazások forgalmat.

A hálózati biztonsági csoportok biztonsági szabályokat megjelenik, miután hozzá kell rendelni az alhálózatot, amely tartalmazza az App Service-környezet szükséges.  A hozzárendelés parancs hivatkozik, mind a virtuális hálózatot, amelyben az App Service-környezetben található nevére, valamint az alhálózat, hol jött létre az App Service-környezet neve.  

Az alábbi példában látható egy alhálózat és virtuális hálózat lesz hozzárendelve hálózati biztonsági csoportok:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Ha a hálózati biztonsági csoport-hozzárendelés sikeres (a hozzárendelés egy hosszú ideig futó műveletek és néhány percet is igénybe vehet), csak a forgalom megfelelő bejövő *engedélyezése* szabályok sikeresen el fog érni az App Service-alkalmazások Környezet.

A teljesség az alábbi példa bemutatja, hogyan távolítsa el, és így nem rendelje hozzá a hálózati biztonsági csoport az alhálózatról:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Explicit IP SSL vonatkozó különleges szempontok
Ha egy alkalmazás explicit IP-SSL-címmel van konfigurálva (alkalmazható *csak* az ASE, amelyek egy nyilvános virtuális IP-cím), az App Service-környezet alapértelmezett IP-címe helyett a HTTP és HTTPS forgalmat, az alhálózatban folyamatok keresztül egy 80-as és 443-astól különböző portok külön készletét.

Az egyes virtuálisgép-pár az összes IP-SSL-cím által használt portok a portál felhasználói felületén az App Service-környezet részletei UX paneljén található.  Jelölje be "az összes beállítás"--> "IP-címek".  A "IP-címek" panel egy tábla összes explicit módon konfigurált IP-SSL címeket tartalmazza a speciális port pár, amellyel minden IP-SSL címhez társított HTTP és HTTPS-forgalom irányítása mellett az App Service Environment.  A port pár, amelyek a DestinationPortRange paraméterek kell használni, amikor szabályok konfigurálása a hálózati biztonsági csoport.

Egy alkalmazásét az ASE IP SSL használatára van konfigurálva, amikor a külső ügyfelek esetében nem jelenik meg, és nem kell aggódnia a speciális pár portleképezést.  Az alkalmazások forgalom általában a konfigurált IP-SSL címre.  A fordítás, a speciális port pár automatikusan történik, belsőleg irányítaná a forgalmat az utolsó szakasza során be az ASE-t tartalmazó alhálózatról. 

## <a name="getting-started"></a>Első lépések
Első lépések az App Service Environment-környezetek, lásd: [App Service Environment bemutatása][IntroToAppServiceEnvironment]

Biztonságos csatlakozás a háttérerőforrásra App Service-környezet az alkalmazások körül részletekért lásd: [biztonságos csatlakozás a háttérerőforrásokhoz egy App Service Environment-környezet][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

