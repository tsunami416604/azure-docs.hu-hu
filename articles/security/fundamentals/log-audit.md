---
title: Azure biztonsági naplózás és naplózás | Microsoft Docs
description: Ismerje meg az Azure-ban elérhető naplókat, valamint az Ön által megszerezhető biztonsági információkat.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: c5ac9daeb741d400a759603c7a3e3e462cc9294f
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398869"
---
# <a name="azure-security-logging-and-auditing"></a>Azure biztonsági naplózás

Az Azure a konfigurálható biztonsági naplózási és naplózási lehetőségek széles skáláját biztosítja, amelyek segítségével azonosíthatja a biztonsági szabályzatokban és mechanizmusokban mutatkozó réseket. Ez a cikk az Azure-ban üzemeltetett szolgáltatások biztonsági naplóinak generálását, gyűjtését és elemzését ismerteti.

> [!Note]
> A cikkben szereplő bizonyos javaslatok megnövelheti az adatok, a hálózat vagy a számítási erőforrások használatát, és növelheti a licenc-vagy előfizetési költségeket.

## <a name="types-of-logs-in-azure"></a>Az Azure-beli naplók típusai

A Felhőbeli alkalmazások számos mozgó részből összetettek. A naplózási adatelemzések segítségével információkat biztosíthat az alkalmazásokról, és segítséget nyújthat a következőket:

- Korábbi problémák elhárítása vagy a lehetséges lehetőségek megelőzése
- Az alkalmazások teljesítményének és karbantartásának javítása
- Olyan műveletek automatizálása, amelyek egyébként manuális beavatkozást igényelnek

Az Azure-naplók a következő típusokra vannak kategorizálva:
* A **vezérlési/felügyeleti naplók** a Azure Resource Manager létrehozási, frissítési és törlési műveleteivel kapcsolatos információkat biztosítanak. További információ: Azure- [tevékenységek naplói](../../azure-monitor/platform/platform-logs-overview.md).

* Az **adatsíkok naplói** az Azure-erőforrások használatának részeként kiváltott eseményekről nyújtanak információt. Ilyen típusú napló például a Windows-eseményrendszer, a biztonság és az alkalmazás naplófájljai egy virtuális gépen (VM) és a Azure Monitoron keresztül konfigurált [diagnosztikai naplókon](../../azure-monitor/platform/platform-logs-overview.md) .

* A **feldolgozott események** információt nyújtanak az Ön nevében feldolgozott elemzett eseményekről/riasztásokról. Ilyen típusú példák olyan [Azure Security Center riasztások](../../security-center/security-center-managing-and-responding-alerts.md) , ahol a [Azure Security Center](../../security-center/security-center-intro.md) feldolgozta és elemezte az előfizetését, és rövid biztonsági riasztásokat biztosít.

A következő táblázat az Azure-ban elérhető naplók legfontosabb típusait sorolja fel:

| Naplókategória | Napló típusa | Használat | Integráció |
| ------------ | -------- | ------ | ----------- |
|[Tevékenységnaplók](../../azure-monitor/platform/platform-logs-overview.md)|Az Azure Resource Manager-erőforrásokon futó események vezérlése|  Betekintést nyújt az előfizetése erőforrásaiban végrehajtott műveletekre.|    REST API, [Azure monitor](../../azure-monitor/platform/platform-logs-overview.md)|
|[Azure-erőforrás-naplók](../../azure-monitor/platform/platform-logs-overview.md)|Az előfizetésben Azure Resource Manager erőforrások működésével kapcsolatos gyakori információk|   Betekintést nyújt az erőforrás által végrehajtott műveletekre.| Azure Monitor|
|[Jelentéskészítés az Azure Active Directoryban](../../active-directory/reports-monitoring/overview-reports.md)|Naplók és jelentések | A felhasználók és csoportok kezelésével kapcsolatos felhasználói bejelentkezési tevékenységek és rendszertevékenység-információk jelentése.|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtual Machines és Cloud Services](../../azure-monitor/learn/quick-collect-azurevm.md)|A Windows Eseménynapló szolgáltatás és a Linux syslog|  Rögzíti a rendszerinformációkat és a naplózási adatait a virtuális gépeken, és átviszi azokat egy tetszőleges Storage-fiókba.|   Windows (Windows Azure Diagnostics [[wad](../../monitoring-and-diagnostics/azure-diagnostics.md)] Storage) és Linux használata a Azure monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|A Storage-naplózási szolgáltatás metrikákat biztosít a Storage-fiókhoz|Betekintést nyújt a nyomkövetési kérelmekbe, elemzi a használati trendeket, és diagnosztizálja a Storage-fiókkal kapcsolatos problémákat.|   REST API vagy az [ügyféloldali kódtár](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Hálózati biztonsági csoport (NSG) folyamatábrái](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON-formátum, a kimenő és a bejövő folyamatok megjelenítése a szabályok alapján|Egy hálózati biztonsági csoporton keresztül megjeleníti a bejövő és kimenő IP-forgalomra vonatkozó információkat.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Alkalmazás-betekintés](../../azure-monitor/app/app-insights-overview.md)|Naplók, kivételek és egyéni diagnosztika|  Egy Application Performance monitoring (APM) szolgáltatást biztosít több platformon futó webes fejlesztőknek.| REST API, [Power bi](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Adatfeldolgozási/biztonsági riasztások](../../security-center/security-center-intro.md)|  Riasztások Azure Security Center Azure Monitor naplók riasztásai|    Biztonsági információkat és riasztásokat biztosít.|  REST API-k, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>A helyszíni SIEM-rendszerekkel való integráció naplózása
A [Security Center riasztások integrálása](../../security-center/security-center-export-data-to-siem.md) azt ismerteti, hogyan lehet szinkronizálni Security Center riasztásokat, az Azure Diagnostics-naplók által gyűjtött virtuális gépek biztonsági eseményeit, valamint az Azure-naplókat az Azure monitor naplók vagy Siem-megoldás segítségével.

## <a name="next-steps"></a>További lépések

- [Naplózás és naplózás](management-monitoring-overview.md): az adatvédelmet a láthatóság fenntartása és az időben gyorsan reagáló riasztások megválaszolásával biztosíthatja.

- [Biztonsági naplózás és naplózási naplók az Azure-](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)ban: a beállítások érvénybe léptetésével biztosíthatja, hogy az Azure-példányok a megfelelő biztonsági és naplózási naplókat gyűjtsék.

- [Webhelycsoport naplózási beállításainak konfigurálása](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): ha Ön webhelycsoport-rendszergazda, kérje le az egyes felhasználói műveletek előzményeit, és az adott dátumtartomány során végrehajtott műveletek előzményeit.

- Keresse meg a naplót a [Microsoft 365 Security Centerben](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance): a Microsoft 365 Security Center segítségével keresse meg az egyesített naplót, és tekintse meg a szervezet felhasználói és rendszergazdai tevékenységeit.
