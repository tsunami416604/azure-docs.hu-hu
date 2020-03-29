---
title: Az Azure forgalomelemzési séma frissítése – 2020. Microsoft dokumentumok
description: Mintalekérdezések új mezőket a Traffic Analytics séma.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 0e9d37e3a89473e59b94168f8f8c80e7a6621107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969058"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>Mintalekérdezések új mezőkkel a Traffic Analytics sémában (2019. augusztusi sémafrissítés)

A [Traffic Analytics napló sémája](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) a következő új mezőkkel bővült: **SrcPublicIPs_s** , **DestPublicIPs_s**, **NSGRule_s**. A következő néhány hónapban a következő régebbi mezők lesznek elavultak: **VMIP_s**, **Subscription_g**, **Region_s**, NSGRules_s , Subnet_s **NSGRules_s**, **VM_s** **NIC_s**, **NIC_s**, **PublicIPs_s** **FlowCount_d**.
Az új mezők információt nyújtanak a forrás- és cél IP-kről, és egyszerűsítik a lekérdezéseket.

Az alábbiakban három példa látható, amelyek bemutatják, hogyan cserélheti le a régi mezőket újakra.

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>NIC_s VM_s Subnet_s Region_s Subscription_g VMIP_s 1. PublicIPs_s PublicIPs_s PublicIPs_s

Nem kell következtetni forrás és a cél esetekben az Azure és a külső nyilvános folyamatok FlowDirection_s az AzurePublic és külsőnyilvános folyamatok kifejezetten. NVA (Network Virtual Appliance) esetén a FlowDirection_s mező nem megfelelő.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```


## <a name="example-2---nsgrules_s"></a>2. példa – NSGRules_s

A korábbi mező formátumú volt: <Index értéke 0)>|<NSG_RULENAME>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

Korábban az NSG és az NSGRules közötti adatok összesítésére használtuk. Most már nem összesítjük. Tehát NSGList_s csak egy NSG-t tartalmaz, és NSGRules_s is csak egy szabályt tartalmaz. Tehát eltávolítottuk a bonyolult formázás itt, és ugyanaz megtalálható más területeken, mint az alábbiakban említett:

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3---flowcount_d"></a>3. példa – FlowCount_d

Mivel nem klub adatok egész NSG, a FlowCount_d egyszerűen AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d.
A fenti 4-ből csak 1 lesz nulla, a többi három pedig 0. És ez azt jelzi, az állapotát és számát a hálózati adapter, ahol a folyamat elfogták.

Ha a folyamat engedélyezve volt, az "Engedélyezett" előtaggal ellátott mezők egyike ki lesz töltve. Máskülönben a rendszer kitölti a "Megtagadva" előtaggal ellátott mezőket.
Ha a folyamat bejövő volt, a rendszer a\_"d" (InFlows_d) típusú " d" típusú mezők egyikét tölti ki. Máshol " OutFlows_d" lesz felnagyít.

Attól függően, hogy a fenti 2 feltételek, tudjuk, hogy melyik egy a 4-ből lesz feltöltve.


## <a name="next-steps"></a>Következő lépések
A gyakran ismételt kérdésekre adott válaszokért olvassa el a [Forgalomelemzés gyakori kérdéseinek megtekintését](traffic-analytics-faq.md) a funkciókkal kapcsolatos részletekmegtekintéséhez lásd: [Forgalomelemzési dokumentáció](traffic-analytics.md)
