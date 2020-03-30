---
title: Azure forgalomelemzési séma | Microsoft dokumentumok
description: Ismerje meg a Traffic Analytics sémáját az Azure hálózati biztonsági csoport folyamatnaplóinak elemzéséhez.
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
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666375"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Séma- és adatösszesítés a Traffic Analytics szolgáltatásban

A Traffic Analytics egy felhőalapú megoldás, amely betekintést nyújt a felhőhálózatok felhasználói és alkalmazási tevékenységébe. A Traffic Analytics elemzi a Network Watcher hálózati biztonsági csoport (NSG) folyamatnaplóit, hogy betekintést nyújtson az Azure-felhőben lévő forgalomáramlásba. A forgalomelemzéssel a következőkre tehet ki:

- Vizualizálja a hálózati tevékenységet az Azure-előfizetései között, és azonosítsa a forró pontokat.
- Azonosítsa a hálózatot fenyegető biztonsági fenyegetéseket, és biztosítsa a hálózatot olyan információkkal, mint a nyílt portok, az internet-hozzáférést megkísérelő alkalmazások és a tisztességtelen hálózatokhoz csatlakozó virtuális gépek.Identify security threats, and secure your network, with information such as open-ports, applications attempting internet access, and virtual machines (VM) connect ing to rogue networks.
- Ismerje meg az Azure-régiók és az internet közötti forgalomáramlási mintákat, hogy optimalizálja a hálózati telepítést a teljesítmény és a kapacitás érdekében.
- A hálózat helytelen konfigurációinak pontos anameddig, ami a hálózat sikertelen kapcsolataihoz vezet.
- A hálózati használat ismertetése bájtban, csomagban vagy folyamatban.

### <a name="data-aggregation"></a>Adatösszesítés

1. A "FlowIntervalStartTime_t" és a "FlowIntervalEndTime_t" közötti NSG-n lévő összes folyamatnaplót a tárfiókban egyperces időközönként blobként rögzíti, mielőtt a Traffic Analytics feldolgozna.
2. A Traffic Analytics alapértelmezett feldolgozási időköze 60 perc. Ez azt jelenti, hogy a Traffic Analytics 60 percenként kiválasztja a blobokat a tárolóból összesítésre. Ha a feldolgozási időköz 10 perc, a Traffic Analytics minden 10 perc után kiválasztja a blobokat a tárfiókból.
3. Az azonos forrás IP-című, célIP-, célport, NSG-név, NSG-szabály, folyamatirány és átviteli réteg protokoll (TCP vagy UDP) protokollal (Megjegyzés: A forrásport összesítésre kizárt) a Traffic Analytics egyetlen folyamatba illeszkedik(Megjegyzés: A forrásport összesítése kizárt) egyetlen folyamatba illeszkedik.
4. Ez az egyetlen rekord van díszítve (Részletek az alábbi szakaszban), és a Traffic Analytics által a Log Analytics.This folyamat akár 1 óra max.
5. FlowStartTime_t mező az ilyen összesített folyamat (ugyanaz a négyrekord) első előfordulását jelzi a "FlowIntervalStartTime_t" és a "FlowIntervalEndTime_t" közötti áramlási napló feldolgozási időköze között.
6. A ta-ban lévő erőforrások esetében a felhasználói felületen jelzett folyamatok az NSG által látott összes folyamat, de a Log Analytics-felhasználó csak az egyetlen, csökkentett rekordot fogja látni. Az összes folyamat megtekintéséhez használja a blob_id mezőt, amelyre a Storage forrásból hivatkozhat. A rekord teljes folyamatszáma megegyezik a blobban látható egyes folyamatok.

Az alábbi lekérdezés segítségével az elmúlt 30 napban a helyszíni folyamatok naplóit is megtekintheti.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
A fent említett lekérdezés ben szereplő folyamatok blobelérési útjának megtekintéséhez használja az alábbi lekérdezést:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

A fenti lekérdezés létrehoz egy URL-t a blob közvetlen eléréséhez. A helyőrzőkkel ellátott URL-cím az alábbi:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>A Traffic Analytics sémában használt mezők
  > [!IMPORTANT]
  > 2019. augusztus 22-én frissült a Traffic Analytics séma. Az új séma külön biztosítja a forrás- és célIP-eket, így a FlowDirection mező egyszerűbbé válik. </br>
  > FASchemaVersion_s 1-ről 2-re frissítve. </br>
  > Elavult mezők: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s FlowCount_d </br>
  > Új mezők: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > 2019. november 22-ig lesznek elérhetők az elavult mezők.

A Traffic Analytics a Log Analytics szolgáltatásra épül, így egyéni lekérdezéseket futtathat a Traffic Analytics által dekorált adatokon, és riasztásokat állíthat be.

Az alábbiakban a séma mezői láthatók, és azok, amelyeket

| Mező | Formátum | Megjegyzések |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | A Traffic Analytics-adatok táblázata
| SubType_s | FlowLog | A folyamatnaplók altípusa. Csak a "FlowLog", egyéb értékek SubType_s a belső működését a termék |
| FASchemaVersion_s |   2   | Séma verzió. Nem tükrözi az NSG flow log verzióját |
| TimeProcessed_t   | Dátum és idő UTC-ben  | Az az időpont, amikor a Traffic Analytics feldolgozta a nyers folyamatnaplókat a tárfiókból |
| FlowIntervalStartTime_t | Dátum és idő UTC-ben |  A folyamatnapló feldolgozási időközének kezdő időpontja. Ez az az idő, amelyből az áramlási intervallumot mérik |
| FlowIntervalEndTime_t | Dátum és idő UTC-ben | A folyamatnapló feldolgozási időközének befejezési időpontja |
| FlowStartTime_t | Dátum és idő UTC-ben |  A folyamat első előfordulása (amely összesítésre kerül) a "FlowIntervalStartTime_t" és a "FlowIntervalEndTime_t" közötti folyamatnapló-feldolgozási intervallumban. Ez a folyamat összesítési logika alapján lesz összesítve |
| FlowEndTime_t | Dátum és idő UTC-ben | A folyamat utolsó előfordulása (amely összesítésre kerül) a "FlowIntervalStartTime_t" és a "FlowIntervalEndTime_t" közötti folyamatnapló-feldolgozási intervallumban. A v2-es folyamatnapló t illeti, ez a mező azt az időpontot tartalmazza, amikor az azonos négyrekordos utolsó folyamat elindult (a nyers folyamatrekordban "B" jelöléssel) |
| FlowType_s |  * IntravNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * KülsőPublic <br> * RosszindulatúFlow <br> * Ismeretlen Magán <br> * Ismeretlen | Meghatározás a táblázat alatti megjegyzésekben |
| SrcIP_s | Forrás IP-címe | Üres lesz az AzureNyilvános és külsőnyilvános folyamatok esetén |
| DestIP_s | Cél IP-cím | Üres lesz az AzureNyilvános és külsőnyilvános folyamatok esetén |
| VMIP_s | A virtuális gép IP-címe | AzureNyilvános és külsőnyilvános folyamatokhoz használatos |
| PublicIP_s | Nyilvános IP-címek | AzureNyilvános és külsőnyilvános folyamatokhoz használatos |
| DestPort_d | Célport | Az a port, amelyen a forgalom bejövő |
| L4Protocol_s  | * T <br> * U  | Szállítási protokoll. T = TCP <br> U = UDP |
| L7Protocol_s  | Protokoll neve | A célportból származtatva |
| FlowDirection_s | * I = Bejövő<br> * O = Kimenő | Az NSG-ből be- és kifelé áramló/kiáramlás iránya áramlási naplószerint |
| FlowStatus_s  | * A = NSG-szabály által engedélyezett <br> * D = Megtagadva az NSG-szabály által  | Az NSG által engedélyezett/nblokkolt áramlás állapota áramlási napló szerint |
| NSGList_s | \<a NSG_NAME-<-<->RESOURCEGROUP_NAME<\/ \/<előfizetés>i> azonosítója | A folyamathoz társított hálózati biztonsági csoport (NSG) |
| NSGRules_s | \<0-as \| \<indexérték)>NSG_RULENAME NSG_RULENAME>flow-irány>\| \<flow-állapot>\| \< \| \<FlowCount ProcessedByRule> |  NSG-szabály, amely engedélyezte vagy megtagadta ezt a folyamatot |
| NSGRule_s | NSG_RULENAME |  NSG-szabály, amely engedélyezte vagy megtagadta ezt a folyamatot |
| NSGRuleType_s | * Felhasználó által definiált * Alapértelmezett |   Az áramlás által használt NSG-szabály típusa |
| MACAddress_s | MAC-cím | Annak a hálózati adapternek a MAC-címe, amelyen a folyamat rögzítésre került |
| Subscription_s | Az Azure virtuális hálózati/hálózati interfész/virtuális gép előfizetése ezen a mezőn van feltöltve | Csak a FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow és UnknownPrivate folyamattípusok esetén alkalmazható (olyan folyamattípusok, ahol csak az egyik oldal az azure) |
| Subscription1_s | Előfizetés azonosítója | Annak a virtuális hálózatnak/ hálózati illesztőnek/virtuális gépnek az előfizetési azonosítója, amelyhez a folyamat forrás IP-címe tartozik |
| Subscription2_s | Előfizetés azonosítója | Annak a virtuális hálózatnak/ hálózati illesztőnek/virtuális gépnek az előfizetési azonosítója, amelyhez a folyamat cél IP-címe tartozik |
| Region_s | A virtuális hálózat/hálózati adapter/virtuális gép Azure-régiója, amelyhez a folyamat IP-címe tartozik | Csak a FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow és UnknownPrivate folyamattípusok esetén alkalmazható (olyan folyamattípusok, ahol csak az egyik oldal az azure) |
| Region1_s | Azure-régió | A virtuális hálózat/hálózati adapter/virtuális gép Azure-régiója, amelyhez a folyamat forrás IP-címe tartozik |
| Region2_s | Azure-régió | A virtuális hálózat Azon Azure-régiója, amelyhez a folyamat cél IP-címe tartozik |
| NIC_s | \<resourcegroup_Name \/ \<>NetworkInterfaceName> |  A virtuális gép küldéséhez vagy fogadását társított NIC |
| NIC1_s | >/NetworkInterfaceName\<> <resourcegroup_Name | A forrás IP-címhez társított nic a folyamatban |
| NIC2_s | >/NetworkInterfaceName\<> <resourcegroup_Name | A cél IP-címhez társított nic a folyamatban |
| VM_s | <>\/ \<NetworkInterfaceName> resourcegroup_Name | A hálózati adapterhez társított virtuális gép NIC_s |
| VM1_s | <resourcegroup_Name\<>/VirtualMachineName> | A forrás IP-címéhez társított virtuális gép a folyamatban |
| VM2_s | <resourcegroup_Name\<>/VirtualMachineName> | A cél IP-címéhez társított virtuális gép a folyamatban |
| Subnet_s | <ResourceGroup_Name>/<\<VNET_Name>/ Alhálózati név> | A NIC_s társított alhálózat |
| Subnet1_s | <ResourceGroup_Name>/<\<VNET_Name>/ Alhálózati név> | A forrás IP-címhez társított alhálózat a folyamatban |
| Subnet2_s | <ResourceGroup_Name>/<\<VNET_Name>/ Alhálózati név>    | A cél IP-címhez társított alhálózat a folyamatban |
| ApplicationGateway1_s | \<SubscriptionId>/\<ResourceGroupName\<>/ApplicationGatewayName> | A forrás IP-címéhez társított alkalmazásátjáró a folyamatban |
| ApplicationGateway2_s | \<SubscriptionId>/\<ResourceGroupName\<>/ApplicationGatewayName> | A cél IP-címéhez társított alkalmazásátjáró a folyamatban |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName\<>/ LoadBalancerName> | A forrás IP-címéhez társított terheléselosztó a folyamatban |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName\<>/ LoadBalancerName> | A cél IP-címéhez társított terheléselosztó a folyamatban |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName\<>/ LocalNetworkGatewayName> | A forrás IP-címéhez társított helyi hálózati átjáró a folyamatban |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName\<>/ LocalNetworkGatewayName> | A cél IP-címéhez társított helyi hálózati átjáró a folyamatban |
| ConnectionType_s | A lehetséges értékek a VNetPeering, a VpnGateway és az ExpressRoute |    Kapcsolattípus |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName\<>/ConnectionName> | Kapcsolat neve. A P2S áramlástípus esetében ez <gateway name>a formátum _<VPN Client IP> |
| ConnectingVNets_s | Virtuális hálózatnevek térelválasztott listája | Hub és küllős topoológia esetén a hub virtuális hálózatok itt lesznek feltöltve |
| Country_s | Kétbetűs országkód (ISO 3166-1 alfa-2) | ExternalPublic típusú folyamattípusfeltöltés. PublicIPs_s mezőben lévő összes IP-cím ugyanazt az országkódot fogja megosztani |
| AzureRegion_s | Az Azure régió helyei | AzurePublic folyamattípushoz feltöltve. PublicIPs_s mezőben lévő összes IP-cím megosztja az Azure-régiót |
| AllowedInFlows_d | | Az engedélyezett bejövő folyamatok száma. Ez azoknak a folyamatoknak a számát jelenti, amelyek ugyanazt a négyrögzített sávos bejövő folyamatot osztották meg a hálózati illesztőbe, amelyen a folyamatot rögzítették. |
| DeniedInFlows_d |  | A megtagadott bejövő folyamatok száma. (Bejövő a hálózati illesztő, amelyen a folyamat elfogták) |
| AllowedOutFlows_d | | Engedélyezett kimenő folyamatok száma (Kimenő a hálózati adapterhez, amelyen a folyamatrögzítés történt) |
| DeniedOutFlows_d  | | Elutasított kimenő folyamatok száma (Kimenő a hálózati adapterhez, amelyen a folyamat rögzítésre került) |
| FlowCount_d | Elavult. Összes folyamat, amely megegyezik ugyanazzal a négy legfeljebb. Az ExternalPublic és az AzurePublic folyamattípusok esetén a számláló tartalmazza a különböző PublicIP-címekből származó folyamatokat is.
| InboundPackets_d | AzNSG-szabályt alkalmazó hálózati adapteren rögzítettként fogadott csomagok | Ez csak az NSG folyamatnapló-séma 2-es verziójához van feltöltve |
| OutboundPackets_d  | AzNSG-szabályt alkalmazó hálózati adapteren rögzítettként küldött csomagok | Ez csak az NSG folyamatnapló-séma 2-es verziójához van feltöltve |
| InboundBytes_d |  Rögzítettként fogadott bájtok azon a hálózati illesztőn, ahol az NSG-szabályt alkalmazták | Ez csak az NSG folyamatnapló-séma 2-es verziójához van feltöltve |
| OutboundBytes_d | Rögzítettként küldött bájtok azon a hálózati illesztőn, ahol az NSG-szabályt alkalmazták | Ez csak az NSG folyamatnapló-séma 2-es verziójához van feltöltve |
| CompletedFlows_d  |  | Ez nem nulla értékkel van feltöltve az NSG folyamatnapló-séma 2-es verziójához. |
| PublicIPs_s | <> \| \<> \| \<>\| \<INBOUND_PACKETS \| \<INBOUND_PACKETS \| \<INBOUND_PACKETS \| \<>OUTBOUND_BYTES>OUTBOUND_PACKETS FLOW_ENDED_COUNT>>PUBLIC_IP INBOUND_PACKETS INBOUND_PACKETS FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT FLOW_STARTED_COUNT>INBOUND_PACKETS INBOUND_BYTES INBOUND_PACKETS | Sávok által elválasztott bejegyzések |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP \| \<<\| \<>\| \<OUTBOUND_BYTES \| \<>\| \<INBOUND_PACKETS \| \<>>FLOW_ENDED_COUNT>FLOW_STARTED_COUNT OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS OUTBOUND_PACKETS>INBOUND_BYTES> | Sávok által elválasztott bejegyzések |
| DestPublicIPs_s | \| \< \| \< \| \< \| \<INBOUND_BYTES \|OUTBOUND_BYTES \|>\<>OUTBOUND_PACKETS>>FLOW_STARTED_COUNT>DESTINATION_PUBLIC_IP <>>>>>FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS FLOW_ENDED_COUNT. \< | Sávok által elválasztott bejegyzések |

### <a name="notes"></a>Megjegyzések

1. Az AzurePublic és külsőnyilvános folyamatok esetén az ügyfél tulajdonában lévő Azure VM IP VMIP_s mezőben van feltöltve, míg a nyilvános IP-címek a PublicIPs_s mezőben vannak feltöltve. A két folyamattípushoz SrcIP_s és DestIP_s mezők helyett VMIP_s és PublicIPs_s kell használni. Az AzurePublic és externalPublicIP-címek esetében tovább összesítjük, így az ügyfélnapló-elemzési munkaterületre bevitt rekordok száma minimális. (Ez a mező hamarosan elavult, és SrcIP_ és DestIP_s kell használnunk attól függően, hogy az azure virtuális gép volt-e a forrás vagy a cél a folyamatban)
1. A folyamattípusok részletei: A folyamatban részt vevő IP-címek alapján a folyamatokat a következő folyamattípusokba kategorizáljuk:
1. IntraVNet – A folyamat BAN LÉVŐ IP-címek is ugyanabban az Azure virtuális hálózatban vannak.
1. InterVNet – A folyamat IP-címei a két különböző Azure virtuális hálózatok.
1. S2S – (helyről helyre) Az egyik IP-cím az Azure virtuális hálózathoz tartozik, míg a másik IP-cím az Azure virtuális hálózathoz VPN-átjárón vagy expressz útvonalon keresztül az Azure virtuális hálózathoz csatlakoztatott ügyfélhálózathoz (helyhez).
1. P2S – (Pont tól helyig) Az egyik IP-cím az Azure virtuális hálózathoz tartozik, míg a másik IP-cím az Azure virtuális hálózathoz VPN-átjárón keresztül az Azure virtuális hálózathoz csatlakoztatott ügyfélhálózathoz (site) tartozik.
1. AzurePublic – Az egyik IP-cím az Azure Virtuális hálózathoz tartozik, míg a másik IP-cím a Microsoft tulajdonában lévő Azure belső nyilvános IP-címekhez tartozik. Az ügyfél tulajdonában lévő nyilvános IP-címek nem lesznek részei ennek a folyamattípusnak. Például minden ügyfél tulajdonában lévő virtuális gép, amely forgalmat küld egy Azure Service (Storage-végpont) lenne kategorizálva az ilyen folyamattípus.
1. ExternalPublic - Az egyik IP-cím az Azure Virtuális hálózathoz tartozik, míg a másik IP-cím egy nyilvános IP-cím, amely nem az Azure-ban található, nem jelent rosszindulatúként az ASC-hírcsatornákban, amelyeket a Traffic Analytics a feldolgozási időközhöz használ " FlowIntervalStartTime_t" és "FlowIntervalEndTime_t".
1. MaliciousFlow - Az egyik IP-címek tartoznak az azure virtuális hálózat, míg a másik IP-cím egy nyilvános IP-cím, amely nem az Azure-ban, és a jelentések szerint rosszindulatú az ASC-hírcsatornák, hogy a Traffic Analytics fogyaszt a feldolgozási időköz között " FlowIntervalStartTime_t" és "FlowIntervalEndTime_t".
1. UnknownPrivate – Az egyik IP-cím az Azure Virtuális hálózathoz tartozik, míg a másik IP-cím az 1918-as rfc-ben meghatározott privát IP-tartományhoz tartozik, és a Traffic Analytics nem tudja leképezni egy ügyfél tulajdonában lévő webhelyre vagy az Azure virtuális hálózatra.
1. Ismeretlen – Nem lehet leképezni az IP-címek egyikét sem az Azure-beli és a helyszíni (helyszíni) ügyféltopológiával a folyamatokban.
1. Egyes mezőneveket s \_vagy \_d-vel fűzünk hozzá. Ezek nem jelölik a forrást és a célt, de jelzik a karakterláncot és a tizedes jegyeket.

### <a name="next-steps"></a>Következő lépések
A gyakran ismételt kérdésekre adott válaszokért olvassa el a [Forgalomelemzés gyakori kérdéseinek megtekintését](traffic-analytics-faq.md) a funkciókkal kapcsolatos részletekmegtekintéséhez lásd: [Forgalomelemzési dokumentáció](traffic-analytics.md)
