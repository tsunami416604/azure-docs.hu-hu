---
title: Az Azure Data Explorer telepítése a virtuális hálózatba
description: Ismerje meg, hogyan telepítheti az Azure Data Explorert a virtuális hálózatra
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b0530ddada68cc9d07753a3b8ab30bff642e26dd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618662"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Az Azure Data Explorer-fürt telepítése a virtuális hálózatba

Ez a cikk ismerteti az erőforrásokat, amelyek egy Azure Data Explorer-fürt telepítésekor egy egyéni Azure virtuális hálózatra. Ez az információ segít a fürt központi telepítésében a virtuális hálózat (VNet) alhálózatában. Az Azure virtuális hálózatokról a [Mi az Azure virtuális hálózat?](/azure/virtual-network/virtual-networks-overview)

   ![virtuális hálózat diagramja](media/vnet-deployment/vnet-diagram.png)

Az Azure Data Explorer támogatja a fürt üzembe helyezését egy alhálózatba a virtuális hálózat (VNet). Ez a funkció lehetővé teszi, hogy:

* [Kényszerítse ki a Network Security Group](/azure/virtual-network/security-overview) (NSG) szabályokat az Azure Data Explorer fürtforgalomban.
* Csatlakoztassa a helyszíni hálózatot az Azure Data Explorer fürt alhálózatához.
* Biztosítsa az adatkapcsolati forrásokat[(Event Hub](/azure/event-hubs/event-hubs-about) és [Event Grid)](/azure/event-grid/overview) [a szolgáltatás végpontjaival.](/azure/virtual-network/virtual-network-service-endpoints-overview)

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Az Azure Data Explorer-fürt elérése a virtuális hálózatban

Az Azure Data Explorer-fürtöt az egyes szolgáltatásokhoz (motor- és adatkezelési szolgáltatásokhoz) a következő IP-címek használatával érheti el:

* **Privát IP:** A virtuális hálózaton belüli fürt eléréséhez használható.
* **Nyilvános IP:** A fürt eléréséhez a virtuális hálózaton kívülről a felügyeleti és figyelési, és a forráscím a kimenő kapcsolatok indult a fürtből.

A szolgáltatás eléréséhez a következő DNS-rekordok jönnek létre: 

* `[clustername].[geo-region].kusto.windows.net`(motor) `ingest-[clustername].[geo-region].kusto.windows.net` (adatkezelés) minden szolgáltatáshoz a nyilvános IP-címhez van rendelve. 

* `private-[clustername].[geo-region].kusto.windows.net`(motor) `private-ingest-[clustername].[geo-region].kusto.windows.net` (adatkezelés) minden szolgáltatáshoz a privát IP-címhez van rendelve.

## <a name="plan-subnet-size-in-your-vnet"></a>Alhálózat méretének megtervezése a virtuális hálózatban

Az Azure Data Explorer-fürt üzemeltetéséhez használt alhálózat mérete nem módosítható az alhálózat üzembe helyezése után. A virtuális hálózatban az Azure Data Explorer minden virtuális géphez egy privát IP-címet és két privát IP-címet használ a belső terheléselosztókhoz (motor- és adatkezelés). Az Azure-hálózat minden alhálózathoz öt IP-címet is használ. Az Azure Data Explorer két virtuális gépet tartalmaz az adatkezelési szolgáltatásszámára. A motorszolgáltatás virtuális gépei felhasználói konfigurációs méretezési kapacitásonként vannak kiépítve.

Az IP-címek teljes száma:

| Használat | Címek száma |
| --- | --- |
| Motorszolgáltatás | 1 példányonként |
| Adatkezelési szolgáltatás | 2 |
| Belső terheléselosztók | 2 |
| Az Azure által fenntartott címek | 5 |
| **Összesen** | **#engine_instances + 9** |

> [!IMPORTANT]
> Az alhálózat méretét előre meg kell tervezni, mivel az Azure Data Explorer telepítése után nem módosítható. Ezért ennek megfelelően tartalékolta az alhálózat méretét.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Szolgáltatásvégpontok az Azure Data Explorerhez való csatlakozáshoz

[Az Azure Service Endpoints](/azure/virtual-network/virtual-network-service-endpoints-overview) lehetővé teszi, hogy biztosítsa az Azure több-bérlős erőforrásokat a virtuális hálózathoz.
Az Azure Data Explorer-fürt alhálózatba való üzembe helyezése lehetővé teszi az [adatkapcsolatok](/azure/event-hubs/event-hubs-about) beállítását az Event Hub vagy [az Event Grid](/azure/event-grid/overview) szolgáltatással, miközben korlátozza az Azure Data Explorer alhálózat alapjául szolgáló erőforrásokat.

> [!NOTE]
> Ha az EventGrid telepítőjét a [Storage-szal](/azure/storage/common/storage-introduction) és az [Event Hub] szolgáltatással használja, az előfizetésben használt tárfiók az Azure Data Explorer alhálózatának szolgáltatásvégpontjaival zárolható, miközben engedélyezi a megbízható Azure platformszolgáltatásokat a [tűzfal konfigurációjában,](/azure/storage/common/storage-network-security)de az Event Hub nem tudja engedélyezni a Service Endpoint szolgáltatást, mivel nem támogatja a megbízható [Azure platformszolgáltatásokat.](/azure/event-hubs/event-hubs-service-endpoints)

## <a name="dependencies-for-vnet-deployment"></a>A virtuális hálózat telepítésének függőségei

### <a name="network-security-groups-configuration"></a>Hálózati biztonsági csoportok konfigurációja

[A hálózati biztonsági csoportok (NSG)](/azure/virtual-network/security-overview) lehetővé teszik a hálózati hozzáférés vezérlését a virtuális hálózaton belül. Az Azure Data Explorer két végpont használatával érhető el: HTTP -k (443) és TDS (1433). A következő NSG-szabályokat úgy kell konfigurálni, hogy lehetővé tegyék a hozzáférést ezekhez a végpontokhoz a fürt kezeléséhez, figyeléséhez és megfelelő működéséhez.

#### <a name="inbound-nsg-configuration"></a>Bejövő NSG-konfiguráció

| **Használat**   | **Honnan**   | **Címzett**   | **Protocol (Protokoll)**   |
| --- | --- | --- | --- |
| Kezelés  |[ADX-kezelési címek](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | ADX-alhálózat:443  | TCP  |
| Állapotfigyelés  | [ADX állapotfigyelési címek](#health-monitoring-addresses)  | ADX-alhálózat:443  | TCP  |
| ADX belső kommunikáció  | ADX-alhálózat: Minden port  | ADX-alhálózat:Minden port  | Összes  |
| Bejövő Azure-terheléselosztó engedélyezése (állapotminta)  | AzureLoadBalancer  | ADX-alhálózat:80 443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Kimenő NSG-konfiguráció

| **Használat**   | **Honnan**   | **Címzett**   | **Protocol (Protokoll)**   |
| --- | --- | --- | --- |
| Az Azure Storage-tól való függőség  | ADX-alhálózat  | Tároló:443  | TCP  |
| Függőség az Azure Data Lake-től  | ADX-alhálózat  | AzureDataLake:443  | TCP  |
| EventHub betöltése és szolgáltatásfigyelés  | ADX-alhálózat  | EventHub:443,5671  | TCP  |
| Metrikák közzététele  | ADX-alhálózat  | AzureMonitor:443 | TCP  |
| Az Azure Monitor konfigurációjának letöltése  | ADX-alhálózat  | [Az Azure Monitor konfigurációs végpontcímei](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| Active Directory (ha van ilyen) | ADX-alhálózat | AzureActiveDirectory:443 | TCP |
| Hitelesítésszolgáltató | ADX-alhálózat | Internet:80 | TCP |
| Belső kommunikáció  | ADX-alhálózat  | ADX-alhálózat:Minden port  | Összes  |
| Portok, amelyek `sql\_request` `http\_request` et használnak, és dugó  | ADX-alhálózat  | Internet:Egyéni  | TCP  |

### <a name="relevant-ip-addresses"></a>Releváns IP-címek

#### <a name="azure-data-explorer-management-ip-addresses"></a>Az Azure Data Explorer felügyeleti IP-címei

| Régió | Címek |
| --- | --- |
| Ausztrália középső régiója | 20.37.26.134 |
| Ausztrália Central2 | 20.39.99.177 |
| Kelet-Ausztrália | 40.82.217.84 |
| Délkelet-Ausztrália | 20.40.161.39 |
| BrazíliaDél | 191.233.25.183 |
| Közép-Kanada | 40.82.188.208 |
| Kelet-Kanada | 40.80.255.12 |
| Közép-India | 40.81.249.251, 104.211.98.159 |
| USA középső régiója | 40.67.188.68 |
| USA középső régiója | 40.89.56.69 |
| Kelet-Ázsia | 20.189.74.103 |
| USA keleti régiója | 52.224.146.56 |
| USA 2. keleti régiója | 52.232.230.201 |
| USA keleti része2 EUAP | 52.253.226.110 |
| Közép-Franciaország | 40.66.57.91 |
| Dél-Franciaország | 40.82.236.24 |
| Kelet-Japán | 20.43.89.90 |
| Nyugat-Japán | 40.81.184.86 |
| Dél-Korea középső régiója | 40.82.156.149 |
| Dél-Korea déli régiója | 40.80.234.9 |
| USA északi középső régiója | 40.81.45.254 |
| Észak-Európa | 52.142.91.221 |
| Dél-Afrika Észak-Afrika | 102.133.129.138 |
| Dél-Afrika Nyugati | 102.133.0.97 |
| USA déli középső régiója | 20.45.3.60 |
| Délkelet-Ázsia | 40.119.203.252 |
| Dél-India | 40.81.72.110, 104.211.224.189 |
| Az Egyesült Királyság déli régiója | 40.81.154.254 |
| Az Egyesült Királyság nyugati régiója | 40.81.122.39 |
| USA nyugati középső régiója | 52.159.55.120 |
| Nyugat-Európa | 51.145.176.215 |
| Nyugat-India | 40.81.88.112, 104.211.160.120 |
| USA nyugati régiója | 13.64.38.225 |
| USA 2. nyugati régiója | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Egészségügyi figyelőcímek

| Régió | Címek |
| --- | --- |
| Ausztrália középső régiója | 191.239.64.128 |
| Ausztrália 2. középső régiója | 191.239.64.128 |
| Kelet-Ausztrália | 191.239.64.128 |
| Délkelet-Ausztrália | 191.239.160.47 |
| Dél-Brazília | 23.98.145.105 |
| Közép-Kanada | 168.61.212.201 |
| Kelet-Kanada | 168.61.212.201 |
| Közép-India | 23.99.5.162 |
| USA középső régiója | 168.61.212.201 |
| USA középső régiója | 168.61.212.201 |
| Kelet-Ázsia | 168.63.212.33 |
| USA keleti régiója | 137.116.81.189 |
| USA 2. keleti régiója | 137.116.81.189 |
| USA keleti része 2 EUAP | 137.116.81.189 |
| Közép-Franciaország | 23.97.212.5 |
| Dél-Franciaország | 23.97.212.5 |
| Kelet-Japán | 138.91.19.129 |
| Nyugat-Japán | 138.91.19.129 |
| Dél-Korea középső régiója | 138.91.19.129 |
| Dél-Korea déli régiója | 138.91.19.129 |
| USA északi középső régiója | 23.96.212.108 |
| Észak-Európa | 191.235.212.69 
| Dél-Afrika Észak-Afrika | 104.211.224.189 |
| Dél-Afrika Nyugati | 104.211.224.189 |
| USA déli középső régiója | 23.98.145.105 |
| Dél-India | 23.99.5.162 |
| Délkelet-Ázsia | 168.63.173.234 |
| Az Egyesült Királyság déli régiója | 23.97.212.5 |
| Az Egyesült Királyság nyugati régiója | 23.97.212.5 |
| USA nyugati középső régiója | 168.61.212.201 |
| Nyugat-Európa | 23.97.212.5 |
| Nyugat-India | 23.99.5.162 |
| USA nyugati régiója | 23.99.5.162 |
| USA nyugati régiója, 2. | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Az Azure Monitor konfigurációs végpontcímei

| Régió | Címek |
| --- | --- |
| Ausztrália középső régiója | 52.148.86.165 |
| Ausztrália 2. középső régiója | 52.148.86.165 |
| Kelet-Ausztrália | 52.148.86.165 |
| Ausztrália Délkelet | 52.148.86.165 |
| Dél-Brazília | 13.68.89.19 |
| Közép-Kanada | 13.90.43.231 |
| Kelet-Kanada | 13.90.43.231 |
| Közép-India | 13.71.25.187 |
| USA középső régiója | 52.173.95.68 |
| USA középső régiója | 13.90.43.231 |
| Kelet-Ázsia | 13.75.117.221 |
| USA keleti régiója | 13.90.43.231 |
| USA 2. keleti régiója | 13.68.89.19 |    
| USA keleti része 2 EUAP | 13.68.89.19 |
| Közép-Franciaország | 52.174.4.112 |
| Dél-Franciaország | 52.174.4.112 |
| Kelet-Japán | 13.75.117.221 |
| Nyugat-Japán | 13.75.117.221 |
| Dél-Korea középső régiója | 13.75.117.221 |
| Dél-Korea déli régiója | 13.75.117.221 |
| USA északi középső régiója | 52.162.240.236 |
| Észak-Európa | 52.169.237.246 |
| Dél-Afrika Észak-Afrika | 13.71.25.187 |
| Dél-Afrika Nyugati | 13.71.25.187 |
| USA déli középső régiója | 13.84.173.99 |
| Dél-India | 13.71.25.187 |
| Délkelet-Ázsia | 52.148.86.165 |
| Az Egyesült Királyság déli régiója | 52.174.4.112 |
| Az Egyesült Királyság nyugati régiója | 52.169.237.246 |
| USA nyugati középső régiója | 52.161.31.69 |
| Nyugat-Európa | 52.174.4.112 |
| Nyugat-India | 13.71.25.187 |
| USA nyugati régiója | 40.78.70.148 |
| USA nyugati régiója, 2. | 52.151.20.103 |

## <a name="expressroute-setup"></a>ExpressRoute beállítása

Az ExpressRoute használatával helyszíni hálózatot csatlakozhat az Azure virtuális hálózathoz. Gyakori beállítás az alapértelmezett útvonal (0.0.0/0) meghirdetése a Border Gateway Protocol (BGP) munkameneten keresztül. Ez arra kényszeríti a virtuális hálózatból érkező forgalmat, hogy továbbítsa az ügyfél telephelyi hálózatára, amely csökkentheti a forgalmat, ami a kimenő folyamatok megszakadását okozhatja. Az alapértelmezett beállítás leküzdése érdekében [a felhasználó által definiált útvonal (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0/0) konfigurálható, és a következő ugrás az *Internet*lesz. Mivel az UDR elsőbbséget élvez a BGP-vel szemben, a forgalmat az internetre szánják.

## <a name="securing-outbound-traffic-with-firewall"></a>Kimenő forgalom biztonságossá tétele tűzfallal

Ha az [Azure Firewall](/azure/firewall/overview) vagy bármely virtuális berendezés használatával szeretné biztonságossá tenni a kimenő forgalmat a tartománynevek korlátozására, a következő teljesen minősített tartományneveket (FQDN) kell engedélyezni a tűzfalon.

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

Az [aszimmetrikus útvonalakkal kapcsolatos](/azure/virtual-network/virtual-networks-udr-overview) problémák megelőzése érdekében meg kell határoznia az alhálózaton a [felügyeleti címeket](#azure-data-explorer-management-ip-addresses) és az [állapotfigyelési címeket](#health-monitoring-addresses) a következő *ugrásinterneten.*

Az USA **nyugati régiójában** például a következő UdR-eket kell definiálni:

| Név | Címelőtag | Következő ugrás |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Az Azure Data Explorer-fürt telepítése a virtuális hálózatba egy Azure Resource Manager-sablon használatával

Az Azure Data Explorer-fürt virtuális hálózatra való üzembe helyezéséhez használja az [Azure Data Explorer-fürt központi telepítését a VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager-sablonba.

Ez a sablon létrehozza a fürtöt, a virtuális hálózatot, az alhálózatot, a hálózati biztonsági csoportot és a nyilvános IP-címeket.
