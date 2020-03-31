---
title: Az Azure AD Connect Health – Az állapotszolgáltatás adatai nem naprakészek | Microsoft dokumentumok
description: Ez a dokumentum ismerteti az "Állapotszolgáltatás adatai nem naprakészek" riasztás okait és a hibaelhárítását.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897178"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Az állapotfigyelő szolgáltatás adatai nem naprakészek

## <a name="overview"></a>Áttekintés

Az ügynökök a helyszíni gépeken, amelyek az Azure AD Connect Health rendszeresen feltölti az adatokat az Azure AD Connect állapotszolgáltatás. Ha a szolgáltatás nem fogad adatokat egy ügynöktől, a portál által megadott információ elavult lesz. A probléma kiemelése érdekében a szolgáltatás felfogja vetni az **állapotszolgáltatás adatai nem naprakész** riasztás. Ez a riasztás akkor jön létre, ha a szolgáltatás nem kapott teljes adatokat az elmúlt két órában.  

- A **figyelmeztetési** állapot riasztás akkor aktiválódik, ha az állapotfigyelő szolgáltatás csak **részleges** adattípusokat kapott a kiszolgálóról az elmúlt két órában. A figyelmeztető állapotriasztás nem indít el e-mail értesítéseket a konfigurált címzetteknek. 
- A **Error** hibaállapot-riasztás akkor aktiválódik, ha az állapotfigyelő szolgáltatás az elmúlt két órában nem kapott adattípust a kiszolgálóról. A hibaállapot-riasztás e-mail értesítéseket vált ki a konfigurált címzetteknek.

A szolgáltatás leveszi az adatokat a helyszíni gépeken futó ügynököktől, a szolgáltatás típusától függően. Az alábbi táblázat felsorolja a számítógépen futó ügynököket, a teendőiket és a szolgáltatás által létrehozott adattípusokat. Bizonyos esetekben több szolgáltatás is részt vesz a folyamatban, így bármelyikük lehet a tettes. 

## <a name="understanding-the-alert"></a>A riasztás ismertetése

A **Riasztás részletei** panel azt mutatja, hogy mikor történt a riasztás, és mikor történt utoljára. A kétóránként futó háttérfolyamat létrehozza és újraértékeli a riasztást. A következő példában a kezdeti riasztás 03/10-kor, 9:59-kor történt. A riasztás még mindig létezett 03/12-én 10:00 órakor, amikor a riasztás újra kilett értékelve. A panel azt is részletezi, hogy az állapotfigyelő szolgáltatás mikor kapott utoljára egy adott adattípust. 
 
 ![Az Azure AD Connect állapotriasztásának részletei](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Az alábbi táblázat a szolgáltatástípusokat a megfelelő szükséges adattípusokhoz rendeli:

| Szolgáltatás típusa | Ügynök (Windows-szolgáltatás neve) | Cél | Létrehozott adattípus  |
| --- | --- | --- | --- |  
| Azure AD Connect (szinkronizálás) | Azure AD Connect Health Sync Insights szolgáltatás | AAD Connect-specifikus információk gyűjtése (összekötők, szinkronizálási szabályok stb.) | - AadSyncService-Szinkronizálási szabályok <br />  - AadSyncService-csatlakozók <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileEredmények <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync Monitoring szolgáltatás | AAD Connect-specifikus perf számlálók, ETW-nyomkövetések, fájlok gyűjtése | Teljesítményszámláló |
| AD DS | Azure AD Connect Health AD DS Insights szolgáltatás | Szintetikus tesztek végrehajtása, topológiaadatok gyűjtése, replikációs metaadatok |  - Hozzáteszi-TopológiaInfo-Json <br /> - Common-TestData-Json (létrehozza a vizsgálati eredmények)   | 
|  | Azure AD Connect Health AD DS Monitoring szolgáltatás | Gyűjtse össze az ADDS-specifikus perf számlálókat, ETW-nyomkövetéseket, fájlokat | - Teljesítmény számláló  <br /> - Common-TestData-Json (feltölti a vizsgálati eredmények)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics szolgáltatás | Szintetikus vizsgálatok elvégzése | TestResult (létrehozza a vizsgálati eredményeket) | 
| | Azure AD Connect Health AD FS Insights szolgáltatás  | ADFS-használati mutatók gyűjtése | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring szolgáltatás | ADFS-specifikus perf számlálók, ETW-nyomkövetések, fájlok gyűjtése | TestResult (feltölti a vizsgálati eredményeket) |

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések 

A probléma diagnosztizálásához szükséges lépések az alábbiakban találhatók. Az első az összes szolgáltatástípusra közös alapvető ellenőrzések készlete. 

> [!IMPORTANT] 
> Ez a riasztás a Connect Health [adatmegőrzési házirendet](reference-connect-health-user-privacy.md#data-retention-policy) követi

* Győződjön meg arról, hogy az ügynökök legújabb verziói vannak telepítve. A [kiadási előzmények](reference-connect-health-version-history.md)megtekintése . 
* Győződjön meg arról, hogy az Azure AD Connect állapotügynökök szolgáltatások **futnak** a gépen. Például a Connect Health for AD FS három szolgáltatással kell rendelkeznie.
  ![Az Azure AD Connect Health ellenőrzése](./media/how-to-connect-health-agent-install/install5.png)

* Győződjön meg róla, hogy menjen át, és megfelelnek a [követelményeknek részben](how-to-connect-health-agent-install.md#requirements).
* A [tesztkapcsolati eszközzel](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) felderítheti a kapcsolódási problémákat.
* Http-proxy használata esetén kövesse az alábbi [konfigurációs lépéseket.](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy) 


## <a name="next-steps"></a>További lépések
Ha a fenti lépések bármelyike problémát talált, javítsa ki, és várja meg, amíg a riasztás megoldódik. A riasztási háttér folyamat 2 óránként fut, így a riasztás feloldása akár 2 órát is igénybe vehet. 

* [Az Azure AD Connect health adatmegőrzési házirendje](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health – gyakori kérdések](reference-connect-health-faq.md)
