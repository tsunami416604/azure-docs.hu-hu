---
title: Bejövő forgalom vezérlése v1
description: Ismerje meg, hogyan vezérelheti a bejövő forgalmat egy App Service Environment. Ez a dokumentum csak az örökölt v1-es szolgáltatót használó ügyfelek számára van megadva.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804401"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Bejövő forgalom vezérlése App Service Environment
## <a name="overview"></a>Áttekintés
App Service Environment hozható létre Azure Resource Manager virtuális hálózaton **vagy** **egy klasszikus** üzemi modell [virtuális hálózatban][virtualnetwork].  Egy új virtuális hálózat és egy új alhálózat is meghatározható a App Service Environment létrehozásakor.  Alternatív megoldásként létrehozhat egy App Service Environment egy meglévő virtuális hálózatban és már meglévő alhálózatban is.  A 2016 júniusában történt módosításokkal a ASE olyan virtuális hálózatokon is üzembe helyezhetők, amelyek nyilvános címtartományt vagy RFC1918 (azaz magánhálózati címeket) használnak.  App Service Environment létrehozásával kapcsolatos további információkért tekintse meg a [app Service Environment létrehozását][HowToCreateAnAppServiceEnvironment]ismertető témakört.

Egy App Service Environmentt mindig létre kell hozni egy alhálózaton belül, mert egy alhálózat hálózati határt biztosít, amely lehetővé teszi a felsőbb rétegbeli eszközök és szolgáltatások mögötti bejövő forgalom zárolását, így a HTTP-és HTTPS-forgalom csak adott felsőbb rétegbeli IP-címekről fogadható el.

Az alhálózatok bejövő és kimenő hálózati forgalmát egy [hálózati biztonsági csoport][NetworkSecurityGroups]vezérli. A bejövő forgalom szabályozásához hálózati biztonsági szabályok létrehozására van szükség egy hálózati biztonsági csoportban, majd hozzá kell rendelni a hálózati biztonsági csoportot a App Service Environment tartalmazó alhálózathoz.

Ha egy hálózati biztonsági csoport hozzá van rendelve egy alhálózathoz, a App Service Environmentban lévő alkalmazásokba bejövő adatforgalom engedélyezett/letiltott a hálózati biztonsági csoportban meghatározott engedélyezési és megtagadás szabályok alapján.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Egy App Service Environmentban használt bejövő hálózati portok
Mielőtt zárolja a bejövő hálózati forgalmat egy hálózati biztonsági csoporttal, fontos tudnia, hogy az App Service Environment által használt kötelező és választható hálózati portok készletét szeretné-e használni.  Bizonyos portok forgalmának véletlen bezárása a App Service Environment funkcióinak elvesztését eredményezheti.

Az alábbi lista az App Service Environment által használt portok listáját tartalmazza. Az összes port a **TCP**, kivéve, ha más egyértelműen feljegyezték:

* 454: az Azure-infrastruktúra által az App Service környezetek TLS-n keresztül történő kezeléséhez és karbantartásához használt **szükséges port** .  Ne blokkolja a forgalmat erre a portra.  Ez a port mindig a bevezetés nyilvános VIP-címéhez van kötve.
* 455: az Azure-infrastruktúra által az App Service környezetek TLS-n keresztül történő kezeléséhez és karbantartásához használt **szükséges port** .  Ne blokkolja a forgalmat erre a portra.  Ez a port mindig a bevezetés nyilvános VIP-címéhez van kötve.
* 80: alapértelmezett port a bejövő HTTP-forgalomhoz App Service-csomagokban futó alkalmazásokban egy App Service Environment.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.
* 443: a bejövő TLS-forgalom alapértelmezett portja App Service-csomagokban futó alkalmazások számára egy App Service Environment.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.
* 21: vezérlési csatorna az FTP-hez.  Ez a port biztonságosan blokkolható, ha az FTP nincs használatban.  A ILB-kompatibilis beadási szolgáltatásban ez a port a ILB-címekhez köthető.
* 990: vezérlési csatorna a FTPS számára.  Ez a port biztonságosan blokkolható, ha a FTPS nincs használatban.  A ILB-kompatibilis beadási szolgáltatásban ez a port a ILB-címekhez köthető.
* 10001-10020: adatcsatornák FTP-hez.  A vezérlési csatornához hasonlóan ezek a portok biztonságosan blokkolva lesznek, ha az FTP nincs használatban.  A ILB-kompatibilis kiegészítő szolgáltatásban ez a port a beadási ILB-címnek is köthető.
* 4016: a Visual Studio 2012 használatával történő távoli hibakereséshez használatos.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nincs használatban.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.
* 4018: a Visual Studio 2013 használatával történő távoli hibakereséshez használatos.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nincs használatban.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.
* 4020: a Visual Studio 2015 használatával történő távoli hibakereséshez használatos.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nincs használatban.  Egy ILB-kompatibilis beadási porton ez a port a ILB-címnek van kötve.

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolat és DNS-követelmények
Ahhoz, hogy egy App Service Environment megfelelően működjön, a különböző végpontokhoz is kimenő hozzáférést igényel. A szolgáltató által használt külső végpontok teljes listája a [ExpressRoute hálózati konfigurációjának](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) "szükséges hálózati kapcsolat" szakaszában található.

App Service környezetekhez a virtuális hálózathoz konfigurált érvényes DNS-infrastruktúra szükséges.  Ha bármilyen okból a DNS-konfiguráció megváltozik egy App Service Environment létrehozása után, a fejlesztők kényszerítheti az App Service Environment az új DNS-konfiguráció felvételére.  A működés közbeni környezet újraindítása a [Azure Portal][NewPortal] app Service Environment felügyelet paneljének tetején található újraindítás ikon használatával a környezet új DNS-konfigurációt fog felvenni.

Azt is javasoljuk, hogy a vnet lévő egyéni DNS-kiszolgálókat a App Service Environment létrehozása előtt előre be kell állítani.  Ha a virtuális hálózat DNS-konfigurációja megváltozik egy App Service Environment létrehozásakor, az App Service Environment létrehozási folyamata sikertelen lesz.  Hasonló módon, ha egy egyéni DNS-kiszolgáló létezik egy VPN-átjáró másik végén, és a DNS-kiszolgáló nem érhető el, vagy nem érhető el, a App Service Environment létrehozási folyamata is sikertelen lesz.

## <a name="creating-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
A hálózati biztonsági csoportok működésével kapcsolatos részletes információkért tekintse meg a következő [információkat][NetworkSecurityGroups].  Az alábbi Azure Service Management-példa megérinti a hálózati biztonsági csoportok kiemelését, és egy hálózati biztonsági csoport konfigurálására és alkalmazására összpontosít egy App Service Environmentt tartalmazó alhálózatra.

**Megjegyzés:** A hálózati biztonsági csoportok az [Azure Portalon](https://portal.azure.com) vagy a Azure PowerShell használatával is konfigurálhatók.

A hálózati biztonsági csoportok először az előfizetéshez társított önálló entitásként jönnek létre. Mivel a hálózati biztonsági csoportok egy Azure-régióban jönnek létre, ügyeljen arra, hogy a hálózati biztonsági csoport ugyanabban a régióban legyen létrehozva, mint a App Service Environment.

A következő egy hálózati biztonsági csoport létrehozását mutatja be:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

A hálózati biztonsági csoport létrehozása után a rendszer egy vagy több hálózati biztonsági szabályt ad hozzá.  Mivel a szabályok halmaza idővel változhat, ajánlott kizárni a szabály prioritásaihoz használt számozási sémát, hogy az idő múlásával könnyebben lehessen további szabályokat beszúrni.

Az alábbi példa egy olyan szabályt mutat be, amely explicit módon hozzáférést biztosít az Azure-infrastruktúra által az App Service Environment felügyeletéhez és karbantartásához szükséges felügyeleti portokhoz.  Vegye figyelembe, hogy az összes felügyeleti forgalom a TLS protokollon keresztül zajlik, és az Ügyféltanúsítványok biztosítják azokat, hogy a portok nyitva legyenek, de az Azure felügyeleti infrastruktúrán kívül más entitások is elérhetetlenné válnak.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Amikor a 80-es és a 443-as porthoz fér hozzá az "elrejtés" App Service Environment a felsőbb rétegbeli eszközök vagy szolgáltatások mögött, ismernie kell a felsőbb rétegbeli IP-címet.  Ha például webalkalmazási tűzfalat (WAF) használ, a WAF saját IP-címe (vagy címe) lesz, amelyet az alsóbb rétegbeli App Service Environment felé irányuló forgalom proxyján használ.  Ezt az IP-címet egy hálózati biztonsági szabály *SourceAddressPrefix* paramétereként kell használni.

Az alábbi példában egy adott felsőbb rétegbeli IP-címről érkező bejövő forgalom explicit módon engedélyezett.  A *1.2.3.4* a felsőbb RÉTEGBELI WAF IP-címének helyőrzőjét használja.  Módosítsa az értéket úgy, hogy az megfeleljen a felsőbb rétegbeli eszköz vagy szolgáltatás által használt címnek.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Ha az FTP-támogatásra van szükség, a következő szabályok használhatók sablonként, amely hozzáférést biztosít az FTP-vezérlő portjához és az adatcsatorna portjaihoz.  Mivel az FTP állapot-nyilvántartó protokoll, előfordulhat, hogy nem tudja átirányítani az FTP-forgalmat egy hagyományos HTTP/HTTPS-tűzfalon vagy proxy eszközön keresztül.  Ebben az esetben a *SourceAddressPrefix* egy másik értékre kell beállítania – például azon fejlesztői vagy központi telepítési gépek IP-címtartomány, amelyeken az FTP-ügyfelek futnak. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Megjegyzés:** az adatcsatorna porttartomány változhat az előzetes verzió időtartama alatt.)

Ha a Visual Studióval végzett távoli hibakeresést használ, a következő szabályok bemutatják, hogyan lehet hozzáférést biztosítani.  A Visual Studio minden támogatott verziójához külön szabály van, mivel mindegyik verzió egy másik portot használ a távoli hibakereséshez.  Az FTP-hozzáféréshez hasonlóan előfordulhat, hogy a Távoli hibakeresési forgalom nem működik megfelelően egy hagyományos WAF vagy proxy eszközön.  A *SourceAddressPrefix* Ehelyett a Visual studiót futtató fejlesztői gépek IP-címeire lehet beállítani.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Hálózati biztonsági csoport társítása egy alhálózathoz
A hálózati biztonsági csoport rendelkezik egy alapértelmezett biztonsági szabállyal, amely megtagadja az összes külső forgalom elérését.  A fent ismertetett hálózati biztonsági szabályok és az alapértelmezett biztonsági szabály kombinálásával a bejövő forgalmat blokkoló eredmény az, hogy csak az *engedélyezési* művelethez társított forráscím-tartományokból érkező forgalom küldhet forgalmat a app Service Environment futó alkalmazásokba.

A hálózati biztonsági csoport biztonsági szabályokkal való feltöltése után hozzá kell rendelni a App Service Environmentt tartalmazó alhálózathoz.  A hozzárendelési parancs arra a virtuális hálózatra hivatkozik, ahol a App Service Environment található, valamint annak az alhálózatnak a nevét, ahol a App Service Environment létrejött.  

Az alábbi példa egy alhálózathoz és virtuális hálózathoz rendelt hálózati biztonsági csoportot mutat be:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

A hálózati biztonsági csoport hozzárendelésének sikeressége után (a hozzárendelés egy hosszan futó művelet, amely eltarthat néhány percig), csak a bejövő adatforgalomnak megfelelő *engedélyezési* szabályok sikeresen elérik az alkalmazásokat a app Service Environment.

A teljesség kedvéért a következő példa azt mutatja be, hogyan távolíthatja el a hálózati biztonsági csoportot az alhálózatról, és így a következőképpen rendelhető hozzá:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Speciális szempontok explicit IP-hez – SSL
Ha egy alkalmazás egy explicit IP-SSL-címmel van konfigurálva ( *csak* a nyilvános VIP-mel rendelkező ASE vonatkozik), a app Service Environment alapértelmezett IP-címének használata helyett a http-és HTTPS-forgalom a 80-es és 443-es portokon kívül más portokon keresztül áramlik az alhálózatba.

Az egyes IP-alapú SSL-címek által használt egyes portok a portál felhasználói felületén találhatók a App Service Environment részletek UX paneljén.  Válassza a "minden beállítás"--> "IP-címek" lehetőséget.  Az "IP-címek" panel a App Service Environment explicit módon konfigurált IP-SSL-címeinek táblázatát jeleníti meg, valamint a speciális portszámot, amely az egyes IP-címekhez kapcsolódó HTTP-és HTTPS-forgalom irányítására szolgál.  Ezt a portszámot kell használni a DestinationPortRange paraméterekhez a hálózati biztonsági csoport szabályainak konfigurálásakor.

Ha egy adatszolgáltatón belüli alkalmazás IP-SSL használatára van konfigurálva, a külső ügyfelek nem látják, és nem kell aggódniuk a speciális port párok leképezésével kapcsolatban.  Az alkalmazások felé irányuló forgalom általában a beállított IP-SSL-címen fog folyni.  A speciális portra való fordítás automatikusan történik a beléptetési forgalomnak a központot tartalmazó alhálózatba való útválasztási utolsó szakasza során. 

## <a name="getting-started"></a>Első lépések
App Service környezetek használatának megkezdéséhez tekintse [meg a app Service Environment bemutatása][IntroToAppServiceEnvironment] című témakört.

Az alkalmazásoknak a háttér-erőforráshoz való biztonságos csatlakozásának App Service Environmentáról a következő témakörben talál további információt: [biztonságos csatlakozás a háttérbeli erőforrásokhoz egy app Service Environment][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

