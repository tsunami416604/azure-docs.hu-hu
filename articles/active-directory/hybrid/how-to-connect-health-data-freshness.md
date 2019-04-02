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
ms.openlocfilehash: 16794dfdcdc6ed9c2effe412237d2681fca4f394
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803292"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Az állapotfigyelő szolgáltatás adatai nem naprakészek riasztás

## <a name="overview"></a>Áttekintés
Az ügynökök a helyszíni gépeken, amely rendszeresen figyeli az Azure AD Connect Health adatokat tölt fel az Azure AD Connect Health szolgáltatással. Ha a szolgáltatás nem kap adatokat az ügynök, a portálon megjelenő információk elavult lesz. Jelölje ki a problémát, hogy a szolgáltatás írandó **az állapotfigyelő szolgáltatás adatai nem naprakészek** riasztás. Ha a szolgáltatás nem kapott befejezett adatok az elmúlt két órában jön létre.  

* A **figyelmeztetés** ügyfélállapot-riasztás akkor aktiválódik, ha az állapotfigyelő szolgáltatás csak kapott **részleges** a kiszolgálóról az elmúlt két órában küldött adattípusokat. Figyelmeztetés állapot nem indítja el a megadott címzetteknek e-mail-értesítéseket. 
* A **hiba** ügyfélállapot-riasztás akkor aktiválódik, ha az állapotfigyelő szolgáltatás nem kapott bármilyen típusú adatokat a kiszolgálóról az elmúlt két órában. Hiba állapota riasztás eseményindítók a beállított címzettek e-mail-értesítések.

A szolgáltatás az adatok lekérése ügynökök, amelyek a helyszíni gépeken futnak. A szolgáltatás típusától függően az alábbi táblázat az ügynököket, amelyeket futtatni a gépen, és az adattípusok a szolgáltatás által előállított módjait. Bizonyos esetekben vannak sokkal több szolgáltatást, így lehet őket a folyamat lehet. 

## <a name="understanding-the-alert"></a>A riasztás ismertetése
A riasztás részleteit tartalmazó panel azt jelzi, hogy az idő, amikor a riasztás kiadása és legutóbb észlelve. A riasztás a generált/újra-evaluated egy háttérfolyamat, amely két óránként fut által. Az alábbi példában a kezdeti riasztás kiadását okozó 03/10 9-kor: 59. Rendelkezik továbbra is 03/12 egyszerre akár 10:00-kor Ha a riasztás értékelése újra.
A panel is részletezi az idő, amikor az állapotfigyelő szolgáltatás által utoljára fogadott lett egy adott adattípus. 
 
 ![Az Azure AD Connect Health riasztás részletei](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Alább a térkép szolgáltatástípusok és a megfelelő szükséges adattípus van.

| Szolgáltatás típusa | Ügynök (Windows-szolgáltatás neve) | Cél | Generált adatok típusa  |
| --- | --- | --- | --- |  
| Azure AD Connect (Sync) | Azure AD Connect Health Sync Insights szolgáltatás | Az AAD Connect konkrét információkat gyűjthet (összekötők, a szinkronizálási szabályok stb.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring szolgáltatás | Fájlok (az AAD Connect adott) gyűjthető teljesítményszámlálókhoz, ETW-nyomkövetések gyűjtése | Teljesítményszámláló |
| AD DS | Azure AD Connect Health AD DS Insights szolgáltatás | Szintetikus tesztek végrehajtása, a replikálási metaadatok topológia információ gyűjtése |  -Ad-TopologyInfo-Json <br /> -Közös-TestData-Json (hoz létre a vizsgálati eredmények)   | 
|  | Azure AD Connect Health AD DS Monitoring szolgáltatás | (ADDS-specifikus) a Teljesítményfigyelő számlálók, ETW-nyomkövetések fájlok gyűjtése | – A teljesítményszámláló  <br /> -Közös-TestData-Json (feltölti a vizsgálati eredmények)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics szolgáltatás | Szintetikus tesztek végrehajtása | TestResult (hoz létre a vizsgálati eredmények) | 
| | Azure AD Connect Health AD FS Insights szolgáltatás  | AD FS használati metrikák gyűjtése | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring szolgáltatás | (AD FS-specifikus) a Teljesítményfigyelő számlálók, ETW-nyomkövetések fájlok gyűjtése | TestResult (feltölti a vizsgálati eredmények) |

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések 

A probléma diagnosztizálásához szükséges lépéseket az alábbiakban található. Az első olyan közös minden szolgáltatástípus alapvető ellenőrzések. Az alábbi táblázat minden egyes szolgáltatás típusa és az adattípus el adott lépéseket felsoroló. 

> [!IMPORTANT] 
> Ez a riasztás a következő Connect Health [adatmegőrzési szabályzat](reference-connect-health-user-privacy.md#data-retention-policy)

* Győződjön meg arról, hogy az ügynökök legfrissebb verziója van telepítve. Nézet [korábbi kiadások](reference-connect-health-version-history.md). 
* Ügyeljen arra, hogy az Azure AD Connect Health-ügynökök szolgáltatások **futó** a gépen. Például a Connect Health for AD FS mindhárom szolgáltatást kell rendelkeznie.
  ![Az Azure AD Connect Health ellenőrzése](./media/how-to-connect-health-agent-install/install5.png)

* Ügyeljen arra, hogy haladnak át, és megfelelnek a [követelmények szakaszt](how-to-connect-health-agent-install.md#requirements).
* Használat [teszt kapcsolódási eszköz](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) kapcsolati hibák felderítésére.
* Ha egy HTTP-proxyt, kövesse az alábbi [konfigurációs lépések](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>További lépések
Ha problémát észlelt, a fenti lépések bármelyike javítást, és várja meg a riasztás megoldásához. A riasztási rétegzést 2 óránként fut, így a a riasztás feloldásához jelenítse akár 2 órát fog igénybe. 

* [Az Azure AD Connect Health adatmegőrzési szabályzata](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health – gyakori kérdések](reference-connect-health-faq.md)
