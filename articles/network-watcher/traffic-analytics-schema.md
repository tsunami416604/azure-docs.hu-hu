---
title: Az Azure traffic analytics séma |} A Microsoft Docs
description: Ismerje meg, elemezheti az Azure-beli hálózati biztonsági csoport folyamatnaplóinak Traffic Analytics sémájával.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: 922e01c26a2cfe24c8b8a32bb8037d9b3b3384c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109120"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>A Traffic Analytics séma és adatok összesítése

A TRAFFIC Analytics egy felhőalapú megoldás, amely a felhőbeli hálózatok felhasználói és alkalmazástevékenységekbe rálátást biztosít a rendszer. A TRAFFIC Analytics elemzi a Network Watcher hálózati biztonsági csoport (NSG) folyamatnaplóit, amelyek az Azure-felhőben adatforgalmat betekintést nyújtanak. A traffic analytics segítségével:

- Hálózati tevékenység vizualizációja az Azure-előfizetések között, és azonosíthatja a hotspotok.
- Biztonsági kockázatok azonosítása, és tegye biztonságossá hálózatát, például a nyitott portok, internet-hozzáférés és a virtual machines (VM) hálózatok támadó csatlakozik megkísérlő alkalmazások adatokkal.
- Ismerje meg, flow forgalmat az Azure-régiók és az internethez, hogy a saját hálózati telepítéséhez a teljesítmény és a kapacitás optimalizálása.
- Azonosíthatja a hálózati konfigurációs hibák és sikertelen kapcsolatokat a hálózaton.
- Hálózati használati bájt, csomagok vagy folyamatok tudja.

### <a name="data-aggregation"></a>Adatösszesítés

1. Egy NSG-t "FlowIntervalStartTime_t" és "FlowIntervalEndTime_t" között található összes Folyamatnaplók rögzítve lesznek a tárfiókban lévő perces időközönként blobként előtt a Traffic Analytics által feldolgozott. 
2. Traffic Analytics feldolgozási időköz alapértelmezett értéke 60 másodperc. Ez azt jelenti, hogy a Traffic Analytics választja ki a BLOB storage összesítés 60 percenként.
3. Folyamatok, amelyek a forrás IP-címe, cél IP-címe, Célport, NSG neve, NSG-szabályt, folyamat iránya, és Transport layer protocol (TCP vagy UDP) (Megjegyzés: Forrásport összesítés ki van zárva) vannak clubbed egy flow-bA a Traffic Analytics
4. Ez egyetlen rekord kitüntetett (a részleteket az alábbi szakaszban), és a Log Analytics által a Traffic Analytics betöltött.
5. FlowStartTime_t jelzi a első előfordulása ilyen egy összesített folyamat (azonos négy rekordos) a folyamat napló feldolgozása "FlowIntervalStartTime_t" és "FlowIntervalEndTime_t" közötti időköz. 
6. A TA bármilyen erőforrás a flow, a felhasználói felületen megadott teljes folyamatok alapegységét az NSG-t, de Log Anlaytics felhasználó csak az egyetlen, csökkentett rekordot fog megjelenni. Összes folyamat megtekintéséhez használja a blob_id mező, amely a Storage-ból lehet rá hivatkozni. Teljes száma, hogy rekord megegyeznek az egyes folyamatok, a blob látható.


### <a name="fields-used-in-traffic-analytics-schema"></a>A Traffic Analytics séma használt mezők

A TRAFFIC Analytics – Naplóelemzés, épül, így a Traffic Analytics által kitüntetett és riasztásokat állíthat be ugyanazon adatok egyéni lekérdezéseket is futtathat.

Az alábbiakban a séma- és azok jelölésére mezői

| Mező | Formátum | Megjegyzések | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tábla Anlaytics forgalmi adatok
| SubType_s | Forgalomnapló | A folyamat-naplók altípusa |
| FASchemaVersion_s |   1   | Scehma verziója. Nem tükrözi az NSG-folyamat napló verziója |
| TimeProcessed_t   | Dátuma és időpontja (UTC)  | Idő, amikor a Traffic Analytics feldolgozásra a nyers Folyamatnaplók a storage-fiókból |
| FlowIntervalStartTime_t | Dátuma és időpontja (UTC) |  A flow log feldolgozási időszakának kezdési időpontját. Ez az időt, amelyből a folyamat időköz mérése |
| FlowIntervalEndTime_t | Dátuma és időpontja (UTC) | Befejezési idő, a flow-log feldolgozási időköze |
| FlowStartTime_t | Dátuma és időpontja (UTC) |  (Ez lesz első vonatkozó összesített érték) a folyamat első előfordulásának a flow log feldolgozási időköze "FlowIntervalStartTime_t" és "FlowIntervalEndTime_t" között található. Ez a folyamat beolvassa összesített összesítési logika alapján |
| FlowEndTime_t | Dátuma és időpontja (UTC) | (Ez lesz első vonatkozó összesített érték) a folyamat utolsó előfordulásának az a folyamat naplója feldolgozási időköze "FlowIntervalStartTime_t" és "FlowIntervalEndTime_t" között. Flow-log v2, tekintetében az idő, amikor az utolsó folyamat az azonos négy rekordos használatába (lett megjelölve "B" a nyers folyamat rekord) tartalmaz a mező |
| FlowType_s |  * IntraVNet <br> * Virtuális hálózatok közötti <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Ismeretlen privát <br> * Ismeretlen | A táblázat alatti megjegyzésekben definíciója |
| SrcIP_s | Forrás IP-címe | AzurePublic esetén üres lesz, és ExternalPublic folyamatok |
| DestIP_s | Cél IP-cím | AzurePublic esetén üres lesz, és ExternalPublic folyamatok |
| VMIP_s | IP-címét a virtuális gép | Használt AzurePublic és ExternalPublic folyamatok |
| PublicIP_S | Nyilvános IP-címek | Használt AzurePublic és ExternalPublic folyamatok |
| DestPort_d | Célport | Amikor a forgalom nem bejövő port | 
| L4Protocol_s  | * T <br> * U  | Átviteli protokoll. T = TCP <br> U = UDP | 
| L7Protocol_s  | Protokollnév | Célport származik |
| FlowDirection_s | * I = bejövő<br> * O = kimenő | A folyamat megfelelően folyamat napló be/ki NSG iránya | 
| FlowStatus_s  | * A Hálózatibiztonságicsoport-szabály által engedélyezett = <br> * D = Hálózatibiztonságicsoport-szabály által megtagadva  | A folyamat engedélyezett/nblocked NSG-t a flow-log megfelelően állapota |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Hálózati biztonsági csoport (NSG) a folyamat társított |
| NSGRules_s | \<A 0 érték indexazonosító) >< NSG_RULENAME >\<folyamat iránya >\<Flow állapot >\<FlowCount ProcessedByRule > |  NSG-szabályt, amely engedélyezi vagy megtagadja a folyamat |
| NSGRuleType_s | * A felhasználó által definiált * alapértelmezett |   A folyamat által használt Hálózatibiztonságicsoport-szabály típusa |
| MACAddress_s | MAC-cím | A hálózati adapter, amelyen a folyamatot rögzítésének MAC-cím |
| Subscription_s | Az Azure virtuális hálózat előfizetése / hálózati adapter / virtuális gép ebben a mezőben van feltöltve. | Csak a FlowType alkalmazható = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow és UnknownPrivate folyamatok típusáról (ahol a csak az egyik oldala az azure flow esetében) |
| Subscription1_s | Előfizetés azonosítója | Virtuális hálózat előfizetés-azonosító / hálózati adapter / virtuális gép, amelyhez a forrás IP-címe a folyamat tartozik |
| Subscription2_s | Előfizetés azonosítója | Virtuális hálózat előfizetés-azonosító / hálózati adapter / virtuális gép, amelyhez a cél IP-címe a folyamat tartozik |
| Region_s | Azure-régió virtuális hálózat / hálózati adapter / virtuális gép, amelyhez a folyamat a IP-cím tartozik | Csak a FlowType alkalmazható = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow és UnknownPrivate folyamatok típusáról (ahol a csak az egyik oldala az azure flow esetében) |
| Region1_s | Azure-régió | Azure-régió virtuális hálózat / hálózati adapter / virtuális gép, amelyhez a forrás IP-címe a folyamat tartozik |
| Region2_s | Azure-régió | Azure-régió virtuális hálózatot, amelyhez a cél IP-címe a folyamat tartozik |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  A virtuális Géphez társított küldése vagy fogadása a forgalom hálózati adapter |
| NIC1_s | < resourcegroup_Name > /\<NetworkInterfaceName > | A forrás IP-címe a folyamathoz társított hálózati adapter |
| NIC2_s | < resourcegroup_Name > /\<NetworkInterfaceName > | A cél IP-cím a folyamathoz társított hálózati adapter |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | A hálózati adapter NIC_s társított virtuális gép |
| VM1_s | < resourcegroup_Name > /\<VirtualMachineName > | A forrás IP-címe a folyamathoz társított virtuális gép |
| VM2_s | < resourcegroup_Name > /\<VirtualMachineName > | A cél IP-cím a folyamathoz társított virtuális gép |
| Subnet_s | < ResourceGroup_Name > / < VNET_Name > /\<SubnetName > | A NIC_s társított alhálózati |
| Subnet1_s | < ResourceGroup_Name > / < VNET_Name > /\<SubnetName > | A folyamat a forrás IP-címmel társított alhálózat |
| Subnet2_s | < ResourceGroup_Name > / < VNET_Name > /\<SubnetName >    | A cél IP-cím a folyamathoz társított alhálózati |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | A forrás IP-címe a folyamathoz társított az Application gateway | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | A cél IP-cím a folyamathoz társított az Application gateway |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | A forrás IP-címe a folyamat hozzárendelve terheléselosztó |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | A folyamat a cél IP-cím hozzárendelve terheléselosztó |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | A forrás IP-címe a folyamathoz társított helyi hálózati átjáró |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | A cél IP-cím a folyamathoz társított helyi hálózati átjáró |
| ConnectionType_s | Lehetséges értékek a következők VNetPeering, a VPN-átjáró és az ExpressRoute |    Kapcsolat típusa |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Kapcsolat neve |
| ConnectingVNets_s | Virtuális hálózat neve szóközzel tagolt listája | Küllős topológia esetén hub virtuális hálózatok elkészül a Itt |
| Country_s | Kétbetűs országkód: (ISO 3166-1 alpha-2) | A folyamat típus ExternalPublic feltöltve. PublicIPs_s mező összes IP-címet oszt megegyező országkódnak |
| AzureRegion_s | Az Azure-régióban helyek | A folyamat típus AzurePublic feltöltve. Összes IP-cím mezőben PublicIPs_s megosztja az Azure-régió |
| AllowedInFlows_d | | Bejövő volt megengedett folyamatok száma. Ez a folyamatok, amelyek az ugyanazon négy rekordos megosztott számát jelöli, amelyen a folyamatot rögzítésének netweork adapterhez bejövő | 
| DeniedInFlows_d |  | Bejövő forgalom, amely el lett utasítva száma. (A hálózati adapterhez, amelyen a folyamatot rögzítésének bejövő) |
| AllowedOutFlows_d | | Kimenő forgalom (a hálózati adapterhez, amelyen a folyamatot rögzítésének kimenő) volt megengedett száma |
| DeniedOutFlows_d  | | (A hálózati adapterhez, amelyen a folyamatot rögzítésének kimenő) el lett utasítva kimenő folyamatok száma |
| FlowCount_d | Elavult. Adatforgalom összesen, amely megfelel a azonos négy-rekord. Folyamatok típusáról ExternalPublic és AzurePublic esetén száma a folyamatok különböző PublicIP címet is tartalmazza.
| InboundPackets_d | A hálózati adaptert, ahol az NSG-szabály lett alkalmazva rögzített fogadott csomagok | Ez csak a séma NSG 2 verzió folyamat kitöltése |
| OutboundPackets_d  | Csomagok küldi be a hálózati adaptert, ahol az NSG-szabály lett alkalmazva | Ez csak a séma NSG 2 verzió folyamat kitöltése |
| InboundBytes_d |  A hálózati adaptert, ahol az NSG-szabály lett alkalmazva rögzített fogadott bájtok száma | Ez csak a séma NSG 2 verzió folyamat kitöltése |
| OutboundBytes_d | Rögzített böngészőállapot a hálózati adaptert, ahol az NSG-szabály lett alkalmazva, elküldött bájtok száma | Ez csak a séma NSG 2 verzió folyamat kitöltése |
| CompletedFlows_d  |  | Ez csak a séma NSG 2 verzió folyamat nullától eltérő értékkel van feltöltve |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Bejegyzés adható sávok |
    
### <a name="notes"></a>Megjegyzések
    
1. AzurePublic és ExternalPublic folyamatok esetén az ügyfél saját Azure virtuális gép IP VMIP_s mezőben van feltöltve, míg a nyilvános IP-címek vannak feltöltését a PublicIPs_s mezőben. Ezen két adatfolyam típusok esetén használandó VMIP_s és PublicIPs_s SrcIP_s és DestIP_s mező helyett. AzurePublic és ExternalPublicIP címét, az azt összesítés tovább, úgy, hogy az ügyfelek log analytics-munkaterület betöltött rekordok száma minimális. (Ez a mező hamarosan elavulttá válik, és azt kell használnia SrcIP_ és DestIP_s attól függően, hogy az azure virtuális gép volt-e a a forrás vagy a cél a flow-ban) 
1. A folyamatok típusáról részletei: A folyamatban érintett IP-címek alapján, hogy kategorizálása a folyamatok, a következő folyamat-típusok: 
1. IntraVNet – mindkét IP-címet a folyamat az azonos Azure virtuális hálózatban található. 
1. Virtuális hálózatok közötti - IP-címek a folyamatot a két különböző Azure virtuális hálózat található. 
1. S2S – az IP-címek (webhelyek) az egyik tartozik Azure Virtual Network közben az IP-cím tartozik ügyfél hálózatán (hely), az Azure Virtual Network VPN-átjárón keresztül vagy Express Route csatlakoztatott. 
1. P2S - (pont hely) az IP-címek egyike tartozik, Azure Virtual Network, amíg az ügyfél hálózatán (hely), az Azure Virtual Network VPN-átjárón keresztül csatlakozik az IP-cím tartozik.
1. AzurePublic – az IP-címek egyike tartozik, Azure Virtual Network közben az IP-cím tartozik, a Microsoft által birtokolt Azure belső, nyilvános IP-címek. Ügyfél tulajdonában lévő nyilvános IP-cím nem része a folyamat típusát. Például minden ügyfél saját VM forgalmat küld egy Azure-szolgáltatás (Storage-végponthoz) lenne kategorizált, ez a folyamat típus alatt. 
1. ExternalPublic – az IP-címek egyike tartozik, az Azure virtuális hálózat egy nyilvános IP-címet, amely nem az Azure-ban az IP-cím pedig nem készül jelentés, a Traffic Analytics feldolgozó között a feldolgozás alatt ASC adatcsatornákban lévő kártevő " FlowIntervalStartTime_t"és"FlowIntervalEndTime_t". 
1. MaliciousFlow – az IP-címek egyike tartozik az azure virtual network egy nyilvános IP-címet, amely nem az Azure-ban és az elvártnak megfelelően az ASC-hírcsatornák, a Traffic Analytics feldolgozó között a feldolgozás alatt lévő kártevő IP-címe pedig" FlowIntervalStartTime_t"és"FlowIntervalEndTime_t". 
1. UnknownPrivate – az IP-címek egyike tartozik Azure virtuális hálózaton az IP-cím tartozik, magánhálózati IP-címtartományt, ahogyan az az RFC 1918, és nem sikerült leképezni a Traffic Analytics által birtokolt, hely vagy az Azure Virtual Network ügyfél számára.
1. Ismeretlen – térkép vagy az IP-címeket a flow a vásárlói topológia az Azure-ban, valamint a helyszíni (hely) nem sikerült.

### <a name="next-steps"></a>További lépések
Válaszok a gyakori kérdésekre, lásd: [Traffic analytics – gyakori kérdések](traffic-analytics-faq.md) funkció kapcsolatos részletekért lásd: [Traffic analytics – dokumentáció](traffic-analytics.md)
    


    


