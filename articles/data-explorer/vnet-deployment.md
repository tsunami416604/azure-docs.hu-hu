---
title: Az Azure Adatkezelő üzembe helyezése a Virtual Network (előzetes verzió)
description: Ismerje meg, hogyan helyezheti üzembe az Azure Adatkezelőt a Virtual Network
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 28b9c55df8cd7883e05e964b8b67e08c7a3eb8c1
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812727"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>Az Azure Adatkezelő üzembe helyezése a Virtual Network (előzetes verzió)

Ez a cikk ismerteti azokat az erőforrásokat, amelyek akkor jelennek meg, amikor Azure Adatkezelő-fürtöt telepít egy egyéni Azure-Virtual Networkba. Ez az információ segítséget nyújt a fürt üzembe helyezéséhez a Virtual Network (VNet) alhálózatában. Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért lásd: [Mi az az azure Virtual Network?](/azure/virtual-network/virtual-networks-overview)

   ![vnet diagram](media/vnet-deployment/vnet-diagram.png)

Az Azure Adatkezelő támogatja a fürtök telepítését a Virtual Network (VNet) alhálózatba. Ez a funkció lehetővé teszi a következőket:

* A [hálózati biztonsági csoport](/azure/virtual-network/security-overview) (NSG) szabályainak érvényesítése az Azure adatkezelő-fürt forgalmán.
* A helyszíni hálózat összekapcsolása az Azure Adatkezelő-fürt alhálózatával.
* Az adatkapcsolati források ([Event hub](/azure/event-hubs/event-hubs-about) és [Event Grid](/azure/event-grid/overview)) biztonságossá tétele [szolgáltatási végpontokkal](/azure/virtual-network/virtual-network-service-endpoints-overview).

> [!NOTE]
> A Virtual Network integrációja és üzembe helyezése előzetes módban van. A szolgáltatás engedélyezéséhez nyisson meg egy [támogatási jegyet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Az Azure Adatkezelő-fürt elérése a VNet

Az Azure Adatkezelő-fürtöt a következő IP-címek használatával érheti el az egyes szolgáltatásokhoz (motor-és adatkezelési szolgáltatások):

* **MAGÁNHÁLÓZATI IP**: a fürtnek a VNet belüli elérésére szolgál.
* **Nyilvános IP-** cím: a fürtnek a VNet kívülről való eléréséhez használatos a felügyelet és a figyelés céljából, valamint a fürtből indított kimenő kapcsolatok forrásaként.

A szolgáltatás eléréséhez a következő DNS-rekordok jönnek létre: 

* a `[clustername].[geo-region].kusto.windows.net` (Engine) `ingest-[clustername].[geo-region].kusto.windows.net` (adatkezelés) az egyes szolgáltatások nyilvános IP-címére van leképezve. 

* `private-[clustername].[geo-region].kusto.windows.net` (Engine) `private-ingest-[clustername].[geo-region].kusto.windows.net` (adatkezelés) minden szolgáltatás magánhálózati IP-címére van leképezve.

## <a name="plan-subnet-size-in-your-vnet"></a>Az alhálózat méretének megtervezése a VNet

Az Azure Adatkezelő-fürt üzemeltetéséhez használt alhálózat mérete nem módosítható az alhálózat telepítése után. A VNet az Azure Adatkezelő egy magánhálózati IP-címet használ minden virtuális géphez, és két magánhálózati IP-címet a belső terheléselosztó (motor és adatkezelés) számára. Az Azure Networking az egyes alhálózatokhoz öt IP-címet is használ. Az Azure Adatkezelő két virtuális gépet foglal le az adatkezelési szolgáltatáshoz. A motor szolgáltatás virtuális gépei felhasználónkénti méretezési kapacitással vannak kiépítve.

Az IP-címek teljes száma:

| Használat | Címek száma |
| --- | --- |
| Motor szolgáltatás | 1/példány |
| Adatkezelési szolgáltatás | 2 |
| Belső terheléselosztók | 2 |
| Azure-beli fenntartott címek | 5 |
| **Teljes** | **#engine_instances + 9** |

> [!IMPORTANT]
> Az alhálózat méretét előre kell tervezni, mivel az Azure Adatkezelő üzembe helyezése után nem módosítható. Ezért a szükséges alhálózati méretet ennek megfelelően kell fenntartani.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Szolgáltatás-végpontok az Azure Adatkezelőhoz való csatlakozáshoz

Az [Azure-szolgáltatás végpontjai](/azure/virtual-network/virtual-network-service-endpoints-overview) lehetővé teszik az Azure több-bérlős erőforrásainak védelmét a virtuális hálózattal.
Az Azure Adatkezelő-fürt az alhálózatba való üzembe helyezése lehetővé teszi az adatkapcsolatok beállítását az [Event hub](/azure/event-hubs/event-hubs-about) vagy a [Event Grid](/azure/event-grid/overview) használatával, miközben korlátozza az Azure adatkezelő alhálózat mögöttes erőforrásait.

> [!NOTE]
> Ha a EventGrid telepítőjét a [Storage](/azure/storage/common/storage-introduction) és az [Event hub] használatával használja, az előfizetésben használt Storage-fiók zárolható az Azure adatkezelő alhálózatához, miközben a megbízható Azure platform-szolgáltatásokat engedélyezi a [tűzfal konfigurációjában](/azure/storage/common/storage-network-security), de az Event hub nem tudja engedélyezni a szolgáltatás-végpontot, mivel az nem támogatja a megbízható [Azure platform-szolgáltatásokat](/azure/event-hubs/event-hubs-service-endpoints).

## <a name="dependencies-for-vnet-deployment"></a>VNet-telepítés függőségei

### <a name="network-security-groups-configuration"></a>Hálózati biztonsági csoportok konfigurálása

A [hálózati biztonsági csoportok (NSG)](/azure/virtual-network/security-overview) lehetővé teszik a VNet belüli hálózati hozzáférés szabályozását. Az Azure Adatkezelő két végponttal érhető el: HTTPs (443) és TDS (1433). A következő NSG szabályokat úgy kell konfigurálni, hogy lehetővé tegyék a fürt felügyeletéhez, figyeléséhez és megfelelő működéséhez való hozzáférést ezekhez a végpontokhoz.

#### <a name="inbound-nsg-configuration"></a>Bejövő NSG-konfiguráció

| **Használat**   | **From**   | **Címzett**   | **Protocol (Protokoll)**   |
| --- | --- | --- | --- |
| Felügyelet  |[ADX-felügyeleti címek](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement (ServiceTag) | ADX alhálózat: 443  | TCP  |
| Állapotfigyelés  | [ADX állapot-figyelési címei](#health-monitoring-addresses)  | ADX alhálózat: 443  | TCP  |
| Belső kommunikáció ADX  | ADX alhálózat: minden port  | ADX alhálózat: minden port  | Mind  |
| Azure Load Balancer bejövő (állapotának) engedélyezése  | AzureLoadBalancer  | ADX alhálózat: 80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Kimenő NSG-konfiguráció

| **Használat**   | **From**   | **Címzett**   | **Protocol (Protokoll)**   |
| --- | --- | --- | --- |
| Függőség az Azure Storage-ban  | ADX alhálózat  | Tárolás: 443  | TCP  |
| Függőség Azure Data Lake  | ADX alhálózat  | AzureDataLake: 443  | TCP  |
| EventHub betöltés és szolgáltatás figyelése  | ADX alhálózat  | EventHub: 443, 5671  | TCP  |
| Metrikák közzététele  | ADX alhálózat  | AzureMonitor: 443 | TCP  |
| Azure Monitor konfiguráció letöltése  | ADX alhálózat  | [Azure monitor konfigurációs végpont címei](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Active Directory (ha van ilyen) | ADX alhálózat | AzureActiveDirectory: 443 | TCP |
| Hitelesítésszolgáltató | ADX alhálózat | Internet: 80 | TCP |
| Belső kommunikáció  | ADX alhálózat  | ADX alhálózat: minden port  | Mind  |
| `sql\_request` és `http\_request` beépülő modulok számára használt portok  | ADX alhálózat  | Internet: egyéni  | TCP  |

### <a name="relevant-ip-addresses"></a>Kapcsolódó IP-címek

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure Adatkezelő felügyeleti IP-címek

| Region (Régió) | Címek |
| --- | --- |
| Ausztrália középső régiója | 20.37.26.134 |
| Ausztráliai Central2 | 20.39.99.177 |
| Ausztrália keleti régiója | 40.82.217.84 |
| Délkelet-Ausztrália | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| Közép-Kanada | 40.82.188.208 |
| Kelet-Kanada | 40.80.255.12 |
| Közép-India | 40.81.249.251 |
| USA középső régiója | 40.67.188.68 |
| USA középső – EUAP | 40.89.56.69 |
| Kelet-Ázsia | 20.189.74.103 |
| USA keleti régiója | 52.224.146.56 |
| USA 2. keleti régiója | 52.232.230.201 |
| Kelet-RÉGIÓJA – EUAP | 52.253.226.110 |
| Közép-Franciaország | 40.66.57.91 |
| Dél-Franciaország | 40.82.236.24 |
| Kelet-Japán | 20.43.89.90 |
| Nyugat-Japán | 40.81.184.86 |
| Korea középső régiója | 40.82.156.149 |
| Dél-Korea | 40.80.234.9 |
| USA északi középső régiója | 40.81.45.254 |
| Észak-Európa | 52.142.91.221 |
| Dél-Afrika északi régiója | 102.133.129.138 |
| Dél-Afrika nyugati régiója | 102.133.0.97 |
| USA déli középső régiója | 20.45.3.60 |
| Délkelet-Ázsia | 40.119.203.252 |
| Dél-India | 40.81.72.110 |
| Egyesült Királyság déli régiója | 40.81.154.254 |
| Egyesült Királyság nyugati régiója | 40.81.122.39 |
| USA nyugati középső régiója | 52.159.55.120 |
| Nyugat-Európa | 51.145.176.215 |
| Nyugat-India | 40.81.88.112 |
| USA nyugati régiója | 13.64.38.225 |
| USA 2. nyugati régiója | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Állapot-figyelési címek

| Region (Régió) | Címek |
| --- | --- |
| Ausztrália középső régiója | 191.239.64.128 |
| Ausztrália 2. középső régiója | 191.239.64.128 |
| Ausztrália keleti régiója | 191.239.64.128 |
| Délkelet-Ausztrália | 191.239.160.47 |
| Dél-Brazília | 23.98.145.105 |
| Közép-Kanada | 168.61.212.201 |
| Kelet-Kanada | 168.61.212.201 |
| Közép-India | 23.99.5.162 |
| USA középső régiója | 168.61.212.201 |
| USA középső – EUAP | 168.61.212.201 |
| Kelet-Ázsia | 168.63.212.33 |
| USA keleti régiója | 137.116.81.189 |
| USA 2. keleti régiója | 137.116.81.189 |
| USA 2. keleti – EUAP | 137.116.81.189 |
| Közép-Franciaország | 23.97.212.5 |
| Dél-Franciaország | 23.97.212.5 |
| Kelet-Japán | 138.91.19.129 |
| Nyugat-Japán | 138.91.19.129 |
| Korea középső régiója | 138.91.19.129 |
| Dél-Korea | 138.91.19.129 |
| USA északi középső régiója | 23.96.212.108 |
| Észak-Európa | 191.235.212.69 
| Dél-Afrika északi régiója | 104.211.224.189 |
| Dél-Afrika nyugati régiója | 104.211.224.189 |
| USA déli középső régiója | 23.98.145.105 |
| Dél-India | 23.99.5.162 |
| Délkelet-Ázsia | 168.63.173.234 |
| Egyesült Királyság déli régiója | 23.97.212.5 |
| Egyesült Királyság nyugati régiója | 23.97.212.5 |
| USA nyugati középső régiója | 168.61.212.201 |
| Nyugat-Európa | 23.97.212.5 |
| Nyugat-India | 23.99.5.162 |
| USA nyugati régiója | 23.99.5.162 |
| USA 2. nyugati régiója | 23.99.5.162 | 

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure Monitor konfigurációs végpont címei

| Region (Régió) | Címek |
| --- | --- |
| Közép-Ausztrália | 52.148.86.165 |
| Közép-Ausztrália, 2 | 52.148.86.165 |
| Kelet-Ausztrália | 52.148.86.165 |
| Délkelet-Ausztrália | 52.148.86.165 |
| Dél-Brazília | 13.68.89.19 |
| Közép-Kanada | 13.90.43.231 |
| Kelet-Kanada | 13.90.43.231 |
| Közép-India | 13.71.25.187 |
| USA középső régiója | 52.173.95.68 |
| USA középső – EUAP | 13.90.43.231 |
| Kelet-Ázsia | 13.75.117.221 |
| USA keleti régiója | 13.90.43.231 |
| USA 2. keleti régiója | 13.68.89.19 | 
| USA 2. keleti – EUAP | 13.68.89.19 |
| Közép-Franciaország | 52.174.4.112 |
| Dél-Franciaország | 52.174.4.112 |
| Kelet-Japán | 13.75.117.221 |
| Nyugat-Japán | 13.75.117.221 |
| Korea középső régiója | 13.75.117.221 |
| Dél-Korea | 13.75.117.221 |
| USA északi középső régiója | 52.162.240.236 |
| Észak-Európa | 52.169.237.246 |
| Dél-Afrika északi régiója | 13.71.25.187 |
| Dél-Afrika nyugati régiója | 13.71.25.187 |
| USA déli középső régiója | 13.84.173.99 |
| Dél-India | 13.71.25.187 |
| Délkelet-Ázsia | 52.148.86.165 |
| Egyesült Királyság déli régiója | 52.174.4.112 |
| Egyesült Királyság nyugati régiója | 52.169.237.246 |
| USA nyugati középső régiója | 52.161.31.69 |
| Nyugat-Európa | 52.174.4.112 |
| Nyugat-India | 13.71.25.187 |
| USA nyugati régiója | 40.78.70.148 |
| USA 2. nyugati régiója | 52.151.20.103 |

## <a name="expressroute-setup"></a>ExpressRoute-telepítő

A ExpressRoute használatával kapcsolódjon a helyszíni hálózathoz az Azure Virtual Networkhoz. Gyakori beállítás az, hogy az alapértelmezett útvonalat (0.0.0.0/0) hirdesse a Border Gateway Protocol (BGP) munkameneten keresztül. Ez arra kényszeríti a Virtual Network érkező forgalmat, hogy az ügyfél helyi hálózatára továbbítsa a forgalmat, így a kimenő folyamatok megszakadnak. A [felhasználó által megadott útvonal (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) leküzdhető, és a következő ugrás az *Internet*lesz. Mivel a UDR elsőbbséget élvez a BGP-vel szemben, a forgalom az internetre kerül.

## <a name="securing-outbound-traffic-with-firewall"></a>A kimenő forgalom védelme tűzfallal

Ha [Azure Firewall](/azure/firewall/overview) vagy bármely virtuális készülék használatával szeretné védeni a kimenő forgalmat a tartománynevek korlátozására, a tűzfalon engedélyezni kell a következő teljes tartományneveket (FQDN).

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

Azt is meg kell határoznia, hogy az alhálózaton lévő [útválasztási táblázat](/azure/virtual-network/virtual-networks-udr-overview) a következő ugrási *internettel* rendelkező [felügyeleti címeket](#azure-data-explorer-management-ip-addresses) és [állapot-figyelési címeket](#health-monitoring-addresses) tartalmazza, hogy megakadályozza az aszimmetrikus útvonalakkal kapcsolatos problémákat.

Az **USA nyugati** régiója esetében például a következő UDR kell definiálni:

| Név | Címelőtag | Következő ugrás |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Azure Adatkezelő-fürt üzembe helyezése a VNet egy Azure Resource Manager-sablon használatával

Ha Azure Adatkezelő-fürtöt szeretne üzembe helyezni a virtuális hálózaton, használja az [azure adatkezelő-fürt üzembe](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) helyezése a VNet Azure Resource Manager-sablonban.

Ez a sablon létrehozza a fürtöt, a virtuális hálózatot, az alhálózatot, a hálózati biztonsági csoportot és a nyilvános IP-címeket.
