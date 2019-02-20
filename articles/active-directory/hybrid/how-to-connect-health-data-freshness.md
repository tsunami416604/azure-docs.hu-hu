---
title: Az Azure AD Connect Health - az állapotfigyelő szolgáltatás adatai nem dátum riasztás legfeljebb |} A Microsoft Docs
description: Ez a dokumentum ismerteti az "Állapotfigyelő szolgáltatás adatai nem naprakészek" riasztás és hibaelhárítás annak okát.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429013"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Az állapotfigyelő szolgáltatás adatai nem naprakészek riasztás

## <a name="overview"></a>Áttekintés
Az ügynökök a helyszíni gépeken, amely rendszeresen figyeli az Azure AD Connect Health adatokat tölt fel az Azure AD Connect Health szolgáltatással. Ha a szolgáltatás nem kap adatokat az ügynök, a portálon megjelenő információk elavult lesz. Jelölje ki a problémát, hogy a szolgáltatás írandó **az állapotfigyelő szolgáltatás adatai nem naprakészek** riasztás. Ezzel létrejön, amikor a szolgáltatás nem kapott adatokat az elmúlt két órában.  

* A **figyelmeztetés** ügyfélállapot-riasztás akkor aktiválódik, ha Connect Health nem kap kiszolgálóról két órán keresztül küldött adatok csak részlegesen elemek. Figyelmeztetés állapot nem indítja el az e-mail értesítések a bérlői rendszergazdával.
* A **hiba** ügyfélállapot-riasztás akkor aktiválódik, ha a Connect Health nem kapja meg a kiszolgáló két órán keresztül küldött adatok elemeket. Hiba állapota riasztás eseményindítók e-mail-értesítéseket a bérlői rendszergazdával.


## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések 

> [!IMPORTANT] 
> Ez a riasztás a következő Connect Health [adatmegőrzési szabályzat](reference-connect-health-user-privacy.md#data-retention-policy)

* Győződjön meg arról, hogy az Azure AD Connect Health-ügynökök szolgáltatások futnak-e a gépen. Például a Connect Health for AD FS mindhárom szolgáltatást kell rendelkeznie.  
  ![Az Azure AD Connect Health ellenőrzése](./media/how-to-connect-health-agent-install/install5.png)

* Ügyeljen arra, hogy haladnak át, és megfelelnek a [követelmények szakaszt](how-to-connect-health-agent-install.md#requirements).
* Használat [teszt kapcsolódási eszköz](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) kapcsolati hibák felderítésére.
* Ha egy HTTP-proxyt, kövesse az alábbi [konfigurációs lépések](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

A riasztás részletei panel hiányzó adatai sorolja fel elem(ek)-kiszolgálóról. Az alábbi táblázat segít a probléma további leszűkíthet. 
### <a name="connect-health-for-sync"></a>Connect Health szinkronizálási szolgáltatás

| Adatelemek | Hibaelhárítási lépések |
| --- | --- | 
| PerfCounter | - [Kimenő kapcsolódás az Azure-szolgáltatási végpont](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Kimenő forgalom SSL-vizsgálata le van tiltva vagy szűrve](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Tűzfalportok az ügynököt futtató kiszolgálón](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - [Kimenő kapcsolódás az Azure-szolgáltatási végpont](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Tűzfalportok az ügynököt futtató kiszolgálón](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>Connect Health for AD FS

Ellenőrizze az AD FS-hez, és kövesse a munkafolyamat a további lépések [AD FS-súgó](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

| Adatelemek | Hibaelhárítási lépések |
| --- | --- | 
| PerfCounter, TestResult | - [Kimenő kapcsolódás az Azure-szolgáltatási végpont](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [Kimenő forgalom SSL-vizsgálata le van tiltva vagy szűrve](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Tűzfalportok az ügynököt futtató kiszolgálón](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
|  Adfs-UsageMetrics | IP-címek, alapuló kimenő kapcsolatok hivatkoznak [Azure IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653) | 

### <a name="connect-health-for-adds"></a>ADDS készült Connect Health

| Adatelemek | Hibaelhárítási lépések |
| --- | --- | 
| PerfCounter, ad-TopologyInfo-Json közös-TestData-Json | - [Kimenő kapcsolódás az Azure-szolgáltatási végpont](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Tűzfalportok az ügynököt futtató kiszolgálón](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |


## <a name="next-steps"></a>További lépések
* [Azure AD Connect Health – gyakori kérdések](reference-connect-health-faq.md)
