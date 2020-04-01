---
title: Az Azure Traffic Analytics sémafrissítése – 2020. Microsoft dokumentumok
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
ms.openlocfilehash: 4fe981576e3f6e58b0886d9c0d2eb2915d8b7720
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396609"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Mintalekérdezések új mezőkkel a Traffic Analytics sémában (2019. augusztusi sémafrissítés)

A [Traffic Analytics naplósémája](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) a következő új mezőket tartalmazza: **SrcPublicIPs_s**, **DestPublicIPs_s**, **NSGRule_s**. Az új mezők információt nyújtanak a forrás- és cél IP-kről, és egyszerűsítik a lekérdezéseket.

A következő néhány hónapban a következő régebbi mezők lesznek elavultak: **VMIP_s**, **Subscription_g**, **Region_s**, NSGRules_s , Subnet_s **NSGRules_s**, **VM_s** **NIC_s**, **NIC_s**, **PublicIPs_s** **FlowCount_d**.

A következő három példa bemutatja, hogyan cserélheti le a régi mezőket az újakra.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>1. példa: VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s NIC_s és PublicIPs_s mezők

Nem kell következtetni forrás és a cél esetekben a **FlowDirection_s** mezőben az AzurePublic és külsőnyilvános folyamatok. Az is nem megfelelő, ha a **FlowDirection_s** mezőt használja egy hálózati virtuális készülékhez.

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

## <a name="example-2-nsgrules_s-field"></a>2. példa: NSGRules_s mező

A régi mező a következő formátumot használta:

<Index értéke 0)>|<NSG_ szabályneve>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

A továbbiakban nem összesítjük az adatokat egy hálózati biztonsági csoport (NSG) között. A frissített sémában **NSGList_s** csak egy NSG-t tartalmaz. Az **NSGRules** is csak egy szabályt tartalmaz. Eltávolítottuk a bonyolult formázást itt és más mezőkben, amint az a példában látható.

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

## <a name="example-3-flowcount_d-field"></a>3. példa: FlowCount_d mező

Mivel nem klub adatok az egész NSG, a **FlowCount_d** egyszerűen:

**AllowedOutFlows_d** + DeniedOutFlows_d**DeniedInFlows_d AllowedOutFlows_d** + **AllowedOutFlows_d** + **AllowedInFlows_d**

A négy mező közül csak egy lesz nem nulla. A másik három mező nulla lesz. A mezők feltöltésével jelzik az állapotot és a számlálót abban a hálózati adapterben, ahol a folyamatot rögzítették.

Ezeknek a feltételeknek a szemléltetésére:

- Ha a folyamat engedélyezve volt, az "Engedélyezett" előtagmezők egyike lesz feltöltve.
- Ha a folyamat megtagadva lett, a rendszer kitölti a "Megtagadva" előtagmezők egyikét.
- Ha a folyamat bejövő volt, a rendszer kitölti az "InFlows_d" utótagmezők egyikét.
- Ha a folyamat kimenő volt, a rendszer a "OutFlows_d" utótag mezők egyikét tölti ki.

A körülményektől függően tudjuk, hogy a négy mező közül melyik lesz feltöltve.

## <a name="next-steps"></a>További lépések

- A gyakori kérdésekre adott válaszokért olvassa el [a Traffic Analytics gyakori kérdések et.](traffic-analytics-faq.md)
- A funkciókkal kapcsolatos részleteket a [Traffic Analytics dokumentációjában](traffic-analytics.md)találja.
