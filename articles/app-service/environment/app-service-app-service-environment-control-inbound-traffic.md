---
title: Bejövő forgalom ellenőrzése v1
description: Ismerje meg, hogyan szabályozhatja az App Service-környezetbe irányuló bejövő forgalmat. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804401"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Az App Service-környezet befelé irányuló forgalom szabályozása
## <a name="overview"></a>Áttekintés
Az App Service-környezet létrehozható **az** Azure Resource Manager virtuális hálózatában **vagy** egy klasszikus üzembe helyezési modell [virtuális hálózatában.][virtualnetwork]  Új virtuális hálózat és új alhálózat definiálható az App Service-környezet létrehozásakor.  Másik lehetőségként egy App Service-környezet is létrehozható egy már meglévő virtuális hálózatban és egy már meglévő alhálózatban.  A 2016 júniusában végrehajtott módosítással az ASE-k olyan virtuális hálózatokba is telepíthetők, amelyek nyilvános címtartományokat vagy RFC1918 címtereket (azaz magáncímeket) használnak.  Az App Service-környezet létrehozásáról további információt az [App Service-környezet létrehozása című témakörben talál.][HowToCreateAnAppServiceEnvironment]

Az App Service-környezetet mindig létre kell hozni egy alhálózaton belül, mert egy alhálózat olyan hálózati határt biztosít, amely a bejövő forgalom zárolására használható a felfelé irányuló eszközök és szolgáltatások mögött, így a HTTP- és HTTPS-forgalmat csak meghatározott upstream IP-címekről fogadják el.

Az alhálózatok bejövő és kimenő hálózati forgalmat [egy hálózati biztonsági csoport][NetworkSecurityGroups]szabályozza. A bejövő forgalom szabályozásához hálózati biztonsági szabályokat kell létrehozni egy hálózati biztonsági csoportban, majd a hálózati biztonsági csoportnak hozzá kell rendelnie az App Service-környezetet tartalmazó alhálózatot.

Miután egy hálózati biztonsági csoport hozzá van rendelve egy alhálózathoz, az App Service-környezetben lévő alkalmazások bejövő forgalma engedélyezett/blokkolva van a hálózati biztonsági csoportban meghatározott engedélyezési és megtagadási szabályok alapján.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Az App Service-környezetben használt bejövő hálózati portok
Mielőtt a bejövő hálózati forgalmat egy hálózati biztonsági csoporttal zárolja, fontos tudni, hogy az App Service-környezet által használt szükséges és választható hálózati portok készlete.  Egyes portok forgalmának véletlen lezárása az App Service-környezetben a funkciók elvesztését eredményezheti.

Az alábbi lista az App Service-környezet által használt portokat tartalmazza. Eltérő rendelkezés hiányában minden port **TCP,** hacsak nincs egyértelműen feltüntetve:

* 454: Az Azure-infrastruktúra által a TLS-en keresztül az App Service-környezetek kezeléséhez és karbantartásához szükséges **port.**  Ne akadályozza a portra irányuló forgalmat.  Ez a port mindig az ASE nyilvános VIP-jéhez van kötve.
* 455: Az Azure-infrastruktúra által a TLS-en keresztül az App Service-környezetek kezeléséhez és karbantartásához szükséges **port.**  Ne akadályozza a portra irányuló forgalmat.  Ez a port mindig az ASE nyilvános VIP-jéhez van kötve.
* 80: Alapértelmezett port az App Service-környezetben az App Service-csomagokban futó alkalmazások bejövő HTTP-forgalmához.  Az ILB-kompatibilis ASE-n ez a port az ASE ILB-címéhez van kötve.
* 443: Alapértelmezett port az App Service-környezetben az App Service-csomagokban futó alkalmazások bejövő TLS-forgalmára.  Az ILB-kompatibilis ASE-n ez a port az ASE ILB-címéhez van kötve.
* 21: Ftp vezérlőcsatornája.  Ez a port biztonságosan blokkolható, ha az FTP nincs használatban.  Az ILB-kompatibilis ASE-n ez a port egy ASE ILB-címéhez köthető.
* 990: FtpS vezérlőcsatornája.  Ez a port biztonságosan blokkolható, ha az FTPS nincs használatban.  Az ILB-kompatibilis ASE-n ez a port egy ASE ILB-címéhez köthető.
* 10001-10020: FTP-adatcsatornák.  A vezérlőcsatornához is, ezek a portok is biztonságosan blokkolhatók, ha az FTP-t nem használják.  Az ILB-kompatibilis ASE-n ez a port az ASE ILB-címéhez köthető.
* 4016: Távoli hibakereséshez használható a Visual Studio 2012-vel.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nincs használatban.  Az ILB-kompatibilis ASE-n ez a port az ASE ILB-címéhez van kötve.
* 4018: Távoli hibakereséshez használható a Visual Studio 2013-mal.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nincs használatban.  Az ILB-kompatibilis ASE-n ez a port az ASE ILB-címéhez van kötve.
* 4020: Távoli hibakereséshez használható a Visual Studio 2015-tel.  Ez a port biztonságosan blokkolható, ha a szolgáltatás nincs használatban.  Az ILB-kompatibilis ASE-n ez a port az ASE ILB-címéhez van kötve.

## <a name="outbound-connectivity-and-dns-requirements"></a>Kimenő kapcsolat és DNS-követelmények
Az App Service-környezet megfelelő működéséhez is szükség van a különböző végpontokhoz való kimenő hozzáférés. Az ASE által használt külső végpontok teljes listája az [ExpressRoute hálózati konfigurációja](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) című cikk "Szükséges hálózati kapcsolat" című részében található.

Az App Service-környezetek hez érvényes DNS-infrastruktúra szükséges a virtuális hálózathoz konfigurálva.  Ha az App Service-környezet létrehozása után bármilyen okból megváltozik a DNS-konfiguráció, a fejlesztők kényszeríthetik az App Service-környezetet az új DNS-konfiguráció felvételére.  Az [Azure-portálon][NewPortal] az App Service-környezet felügyeleti paneljének tetején található "Újraindítás" ikonnal indított működés közbeni környezet újraindítása hatására a környezet felveszi az új DNS-konfigurációt.

Azt is javasoljuk, hogy a virtuális hálózat minden egyéni DNS-kiszolgálóit az App Service-környezet létrehozása előtt előzetesen állítsa be.  Ha egy virtuális hálózat DNS-konfigurációja megváltozik az App Service-környezet létrehozása közben, az az App Service-környezet létrehozási folyamatának sikertelensítését eredményezi.  Hasonló ha egy VPN-átjáró másik végén létezik egyéni DNS-kiszolgáló, és a DNS-kiszolgáló nem érhető el vagy nem érhető el, az App Service-környezet létrehozási folyamata is sikertelen lesz.

## <a name="creating-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
A hálózati biztonsági csoportok működéséről az alábbi [információkat][NetworkSecurityGroups]találja.  Az alábbi Azure Service Management példa a hálózati biztonsági csoportok főbb pontjait érinti, és egy hálózati biztonsági csoport konfigurálására és alkalmazására összpontosít egy App Service-környezetet tartalmazó alhálózatra.

**Megjegyzés:** A hálózati biztonsági csoportok grafikusan konfigurálhatók az [Azure Portalon](https://portal.azure.com) vagy az Azure PowerShellen keresztül.

A hálózati biztonsági csoportok először egy előfizetéshez társított önálló entitásként jönnek létre. Mivel a hálózati biztonsági csoportok egy Azure-régióban jönnek létre, győződjön meg arról, hogy a hálózati biztonsági csoport ugyanabban a régióban jön létre, mint az App Service-környezet.

Az alábbiakban bemutatjuk a hálózati biztonsági csoport létrehozását:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

A hálózati biztonsági csoport létrehozása után egy vagy több hálózati biztonsági szabály kerül hozzáadásra.  Mivel a szabálykészlet idővel változhat, ajánlott a szabályprioritásokhoz használt számozási sémát kicserélni, hogy idővel könnyebben szúrjon be további szabályokat.

Az alábbi példa egy olyan szabályt mutat be, amely kifejezetten hozzáférést biztosít az Azure-infrastruktúra által az App Service-környezet kezeléséhez és karbantartásához szükséges felügyeleti portokhoz.  Vegye figyelembe, hogy az összes felügyeleti forgalom a TLS-en keresztül áramlik, és ügyféltanúsítványok biztosítják, így annak ellenére, hogy a portok meg vannak nyitva, azOkat az Azure felügyeleti infrastruktúrától eltérő entitás nem érhető el.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Amikor zárolja a hozzáférést a 80-as és 443-as porthoz, hogy "elrejtse" az App Service-környezetet az upstream eszközök vagy szolgáltatások mögött, ismernie kell a felső csoportBAN lévő IP-címet.  Ha például webalkalmazás-tűzfalat (WAF) használ, a WAF saját IP-címmel (vagy címekkel) rendelkezik, amelyet a forgalom alsóbb rétegbeli App Service-környezetbe történő proxyzásakor használ.  Ezt az IP-címet egy hálózati biztonsági szabály *SourceAddressPrefix* paraméterében kell használnia.

Az alábbi példában egy adott upstream IP-címről érkező bejövő forgalom explicit módon engedélyezett.  Az *1.2.3.4* címet egy upstream WAF IP-címének helyőrzőjeként használják.  Módosítsa az értéket úgy, hogy az megegyezzen a felső áramszintről használt eszközzel vagy szolgáltatással.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Ha FTP-támogatásra van szükség, a következő szabályok sablonként használhatók az FTP-vezérlőporthoz és az adatcsatorna-portokhoz való hozzáférés biztosításához.  Mivel az FTP állapotalapú protokoll, előfordulhat, hogy nem tudja az FTP-forgalmat hagyományos HTTP/HTTPS tűzfalon vagy proxyeszközön keresztül irányítani.  Ebben az esetben a *SourceAddressPrefix-et* egy másik értékre kell állítania , például azon fejlesztői vagy központi telepítési gépek IP-címtartományára, amelyeken az FTP-ügyfelek futnak. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Megjegyzés:** az adatcsatorna-port tartománya az előnézeti időszakban változhat.)

Ha távoli hibakeresést használ a Visual Studio szolgáltatással, az alábbi szabályok bemutatják a hozzáférés engedélyezését.  A Visual Studio minden támogatott verziójára külön szabály van, mivel minden verzió más-más portot használ a távoli hibakereséshez.  Az FTP-hozzáféréshez megfelelően előfordulhat, hogy a távoli hibakeresési forgalom nem folyik megfelelően egy hagyományos WAF- vagy proxyeszközön keresztül.  A *SourceAddressPrefix* ehelyett a Visual Studio alkalmazást futtató fejlesztői gépek IP-címtartományára állítható be.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Hálózati biztonsági csoport hozzárendelése alhálózathoz
A hálózati biztonsági csoport alapértelmezett biztonsági szabálya megtagadja a hozzáférést az összes külső forgalomhoz.  A fent leírt hálózati biztonsági szabályok és a bejövő forgalmat blokkoló alapértelmezett biztonsági szabály kombinálásának eredménye, hogy csak az *Engedélyezési* művelethez társított forráscímtartományokból származó forgalom képes forgalmat küldeni az App Service-környezetben futó alkalmazásoknak.

Miután egy hálózati biztonsági csoportot felnépesített a biztonsági szabályok, hozzá kell rendelni az App Service-környezetet tartalmazó alhálózathoz.  A hozzárendelési parancs hivatkozik mind a virtuális hálózat nevét, ahol az App Service-környezet található, valamint az alkalmazásszolgáltatási környezetet létrehozó alhálózat nevét.  

Az alábbi példa egy alhálózathoz és virtuális hálózathoz rendelt hálózati biztonsági csoportot mutat be:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Miután a hálózati biztonsági csoport hozzárendelése sikeres (a hozzárendelés egy hosszú ideig futó műveletek, és eltarthat néhány percig), csak a bejövő forgalom megfelelő *engedélyezési* szabályok sikeresen eléri az alkalmazásokat az App Service-környezetben.

A teljesség érdekében a következő példa bemutatja, hogyan távolíthatja el és ezáltal a hálózati biztonsági csoportot az alhálózatról:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Az explicit IP-SSL speciális szempontjai
Ha egy alkalmazás explicit IP-SSL-címmel van konfigurálva *(csak* a nyilvános VIRTUÁLIS IP-vel rendelkező ase-kre vonatkozik), az App Service-környezet alapértelmezett IP-címét használva a HTTP és a HTTPS-forgalom is az alhálózatba áramlik a 80-as és 443-as portoktól eltérő portokon keresztül.

Az egyes IP-SSL-címek által használt portpár megtalálható a portál felhasználói felületén az App Service-környezet részletes UX paneljéről.  Válassza a "Minden beállítás" --> "IP-címek".  Az "IP-címek" panelen látható az App Service-környezet összes explicitmódon konfigurált IP-SSL-címének táblája, valamint az egyes IP-SSL-címekhez társított HTTP- és HTTPS-forgalom továbbítására használt speciális portpár.  Ez az a portpár, amelyet a DestinationPortRange paraméterekhez kell használni a szabályok hálózati biztonsági csoportban történő konfigurálásakor.

Ha egy ASE-n lévő alkalmazás IP-SSL használatára van konfigurálva, a külső ügyfelek nem fogják látni, és nem kell aggódniuk a speciális portpár-hozzárendelés miatt.  Az alkalmazásokba irányuló forgalom általában a beállított IP-SSL-címre fog áramlani.  A speciális portpárra történő fordítás automatikusan történik az ASE-t tartalmazó alhálózatba irányuló útválasztási forgalom utolsó szakasza során. 

## <a name="getting-started"></a>Első lépések
Az App Service-környezetek ismerkedése az [App Service-környezet bemutatása című témakörben][IntroToAppServiceEnvironment]

Az App Service-környezetből a háttér-erőforráshoz biztonságosan csatlakozó alkalmazásokról a [Háttérszolgáltatás-környezetből][SecurelyConnecttoBackend] való biztonságos csatlakozás című témakörben talál további részleteket.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

