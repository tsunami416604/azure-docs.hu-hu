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
ms.openlocfilehash: 3ffd783ec41b1b0c4a11ee426648c1e36fbbbf75
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883700"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Az állapotfigyelő szolgáltatás adatai nem naprakészek riasztás

## <a name="overview"></a>Áttekintés

Az ügynökök a helyszíni gépeken, az Azure AD Connect Health rendszeresen figyeli az Azure AD Connect Health szolgáltatás feltölteni az adatokat. Ha a szolgáltatás nem kap adatokat az ügynök, az adatokat, a portál megjelenít elavult lesz. Jelölje ki a problémát, hogy a szolgáltatás kiváltja a **az állapotfigyelő szolgáltatás adatai nem naprakészek** riasztás. Ez a riasztás akkor jön létre, ha a szolgáltatás nem kapott mindazok az adatok az elmúlt két órában.  

- A **figyelmeztetés** ügyfélállapot-riasztás akkor aktiválódik, ha az állapotfigyelő szolgáltatás csak kapott **részleges** a kiszolgálóról az elmúlt két órában küldött adattípusokat. A figyelmeztetés állapot nem indítja el a megadott címzetteknek e-mail-értesítéseket. 
- A **hiba** ügyfélállapot-riasztás akkor aktiválódik, ha az állapotfigyelő szolgáltatás nem kapott bármilyen típusú adatokat a kiszolgálóról az elmúlt két órában. A hiba állapot riasztási eseményindítók a beállított címzettek e-mail-értesítések.

A szolgáltatás az adatok lekérése a helyszíni gépeken, a szolgáltatás típusától függően futtató ügynökök. Az alábbi táblázat az ügynökök arról, hogy a gép, mit tesznek azt, és az adattípusok, amelyek a szolgáltatás állít elő. Bizonyos esetekben vannak több szolgáltatást a folyamat, így ezek közül bármelyik a probléma oka lehet. 

## <a name="understanding-the-alert"></a>A riasztás ismertetése

A **riasztás részletei** panelen jelenik meg, ha a riasztás történt, és a legutóbb észlelt. Háttérben futó folyamat, amely két óránként állít elő, és értékelje ki ismétlődően a riasztás. A következő példában a kezdeti riasztás történt a 03/10 9-kor: 59. A riasztás még létezik a 03/12/10:00 órakor Ha a riasztás értékelése újra. A panel is részletezi az idő, az állapotfigyelő szolgáltatás utoljára fogadott adattípust. 
 
 ![Az Azure AD Connect Health riasztás részletei](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Az alábbi táblázat szükséges adatok típusával azonos szolgáltatástípusok képez le:

| Szolgáltatás típusa | Ügynök (Windows-szolgáltatás neve) | Cél | Generált adatok típusa  |
| --- | --- | --- | --- |  
| Azure AD Connect (Sync) | Azure AD Connect Health Sync Insights szolgáltatás | Gyűjtsön AAD Connect-specifikus információkat (összekötők, szinkronizálási szabályait, stb.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring szolgáltatás | AAD Connect-specifikus gyűjthető teljesítményszámlálókhoz, ETW-nyomkövetések, fájlok gyűjtése | Teljesítményszámláló |
| AD DS | Azure AD Connect Health AD DS Insights szolgáltatás | Szintetikus tesztek végrehajtása, a replikálási metaadatok topológia információ gyűjtése |  -Ad-TopologyInfo-Json <br /> -Közös-TestData-Json (hoz létre a vizsgálati eredmények)   | 
|  | Azure AD Connect Health AD DS Monitoring szolgáltatás | ADDS-specifikus gyűjthető teljesítményszámlálókhoz, ETW-nyomkövetések, fájlok gyűjtése | – A teljesítményszámláló  <br /> -Közös-TestData-Json (feltölti a vizsgálati eredmények)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics szolgáltatás | Szintetikus tesztek végrehajtása | TestResult (hoz létre a vizsgálati eredmények) | 
| | Azure AD Connect Health AD FS Insights szolgáltatás  | AD FS használati metrikák gyűjtése | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring szolgáltatás | AD FS-specifikus gyűjthető teljesítményszámlálókhoz, ETW-nyomkövetések, fájlok gyűjtése | TestResult (feltölti a vizsgálati eredmények) |

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
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](reference-connect-health-faq.md)
