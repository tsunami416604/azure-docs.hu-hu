---
title: Az Azure AD Connect Health - az állapotfigyelő szolgáltatás adatai nem dátum riasztás legfeljebb |} A Microsoft Docs
description: Ez a dokumentum ismerteti az "Állapotfigyelő szolgáltatás adatai nem naprakészek" riasztás és hibaelhárítás annak okát.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: e470a44732b881311eacecfdf2bd2211598d880a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984860"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Az állapotfigyelő szolgáltatás adatai nem naprakészek riasztás

## <a name="overview"></a>Áttekintés
<li>Az Azure AD Connect Health adatok friss riasztást állít elő, ha azt nem kapott összes adatponttal a kiszolgáló két órán keresztül. A riasztás címét van **az állapotfigyelő szolgáltatás adatai nem naprakészek**. </li>
<li>A **figyelmeztetés** ügyfélállapot-riasztás akkor aktiválódik, ha Connect Health nem kap kiszolgálóról két órán keresztül küldött adatok csak részlegesen elemek. Figyelmeztetés állapot nem indítja el az e-mail értesítések a bérlői rendszergazdával. </li>
<li>A **hiba** ügyfélállapot-riasztás akkor aktiválódik, ha a Connect Health nem kapja meg a kiszolgáló két órán keresztül küldött adatok elemeket. Hiba állapota riasztás eseményindítók e-mail-értesítéseket a bérlői rendszergazdával. </li>

>[!IMPORTANT] 
> Ez a riasztás a következő Connect Health [adatmegőrzési szabályzat](reference-connect-health-user-privacy.md#data-retention-policy)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések 
* Ügyeljen arra, hogy haladnak át, és megfelelnek a [követelmények szakaszt](how-to-connect-health-agent-install.md#requirements).
* Használat [teszt kapcsolódási eszköz](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) kapcsolati hibák felderítésére.
* Ha a HTTP-Proxy, kövesse [konfigurációs lépéseket Itt](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

### <a name="connect-health-for-adfs"></a>Connect Health for AD FS
Ellenőrizze az AD FS-hez, és kövesse a munkafolyamat a további lépések [AD FS-súgó](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

### <a name="data-collection-map-required-steps"></a>Az adatgyűjtés képezze le a szükséges lépések
| Szolgáltatásnév | Adatelemek | Hibaelhárítási lépések |
| --- | --- | --- | 
| AD FS-hez készült Connect Health | PerfCounter, TestResult | - [Kimenő kapcsolódás az Azure-szolgáltatási végpont](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Kimenő forgalom SSL-vizsgálata le van tiltva vagy szűrve](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Tűzfalportok az ügynököt futtató kiszolgálón](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [A kijelölt webhelyek engedélyezése, ha engedélyezve van az Internet Explorer – fokozott biztonsági beállításai](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | AD FS-UsageMetrics | IP-címek, alapuló kimenő kapcsolatok hivatkoznak [Azure IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) | 
| Connect Health szinkronizálási szolgáltatás | PerfCounter | - [Kimenő kapcsolódás az Azure-szolgáltatási végpont](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Kimenő forgalom SSL-vizsgálata le van tiltva vagy szűrve](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Tűzfalportok az ügynököt futtató kiszolgálón](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [A kijelölt webhelyek engedélyezése, ha engedélyezve van az Internet Explorer – fokozott biztonsági beállításai](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
|  | AadSyncService-SynchronizationRules, <br /> AadSyncService-összekötők <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | – IP-címek alapján kimenő kapcsolat hivatkoznak [Azure IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) <br /> - [Kimenő kapcsolódás az Azure-szolgáltatási végpont](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Tűzfalportok az ügynököt futtató kiszolgálón](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 
| ADDS készült Connect Health  | PerfCounter, ad-TopologyInfo-Json közös-TestData-Json | - [Kimenő kapcsolódás az Azure-szolgáltatási végpont](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> - [Kimenő forgalom SSL-vizsgálata le van tiltva vagy szűrve](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Tűzfalportok az ügynököt futtató kiszolgálón](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) <br /> - [A kijelölt webhelyek engedélyezése, ha engedélyezve van az Internet Explorer – fokozott biztonsági beállításai](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) <br />  – IP-címek alapján kimenő kapcsolat hivatkoznak [Azure IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653)  |




## <a name="next-steps"></a>További lépések
* [Azure AD Connect Health – gyakori kérdések](reference-connect-health-faq.md)
