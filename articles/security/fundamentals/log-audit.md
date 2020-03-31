---
title: Az Azure biztonsági naplózása és naplózása | Microsoft dokumentumok
description: Ismerje meg az Azure-ban elérhető naplókat és a biztonsági elemzéseket.
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
ms.openlocfilehash: bd0f42507e22559690e2682a391c53b9c090aa6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750788"
---
# <a name="azure-security-logging-and-auditing"></a>Az Azure biztonsági naplózása és naplózása

Az Azure konfigurálható biztonsági naplózási és naplózási lehetőségek széles skáláját kínálja a biztonsági szabályzatok és mechanizmusok hiányosságainak azonosításához. Ez a cikk ismerteti az Azure-ban üzemeltetett szolgáltatások biztonsági naplóinak generálását, gyűjtését és elemzését.

> [!Note]
> A cikkben szereplő bizonyos javaslatok növelhetik az adat-, hálózati vagy számítási erőforrás-használatot, és növelhetik a licenc- vagy előfizetési költségeket.

## <a name="types-of-logs-in-azure"></a>Naplók típusai az Azure-ban

A felhőalapú alkalmazások összetettek, sok mozgó alkatrészrel. Az adatok naplózása betekintést nyújthat az alkalmazásokba, és segítséget nyújthat:

- A múltbeli problémák elhárítása vagy a lehetséges problémák megelőzése
- Az alkalmazások teljesítményének vagy karbantarthatóságának javítása
- Automatizálja azokat a műveleteket, amelyek egyébként kézi beavatkozást igényelnének

Az Azure-naplók a következő típusokba vannak kategorizálva:
* **A vezérlő/felügyeleti naplók** az Azure Resource Manager CREATE, UPDATE és DELETE műveletekkel kapcsolatos információkat tartalmaznak. További információ: [Azure-tevékenységnaplók.](../../azure-monitor/platform/platform-logs-overview.md)

* **Az adatsík-naplók** az Azure-erőforrások használatának részeként emelt eseményekről nyújtanak információt. Ilyen típusú napló példák a Windows eseményrendszer, a biztonság és az alkalmazásnaplók egy virtuális gép (VM) és a [diagnosztikai naplók,](../../azure-monitor/platform/platform-logs-overview.md) amelyek az Azure Monitoron keresztül konfigurálva vannak.

* **A feldolgozott események** az Ön nevében feldolgozott elemzett eseményekről/riasztásokról nyújtanak információt. Ilyen típusúpéldául az [Azure Security Center riasztásai,](../../security-center/security-center-managing-and-responding-alerts.md) ahol az [Azure Security Center](../../security-center/security-center-intro.md) feldolgozta és elemezte az előfizetést, és tömör biztonsági riasztásokat biztosít.

Az alábbi táblázat az Azure-ban elérhető legfontosabb naplótípusokat sorolja fel:

| Naplókategória | Napló típusa | Használat | Integráció |
| ------------ | -------- | ------ | ----------- |
|[Tevékenységnaplók](../../azure-monitor/platform/platform-logs-overview.md)|Vezérlősíkesemények az Azure Resource Manager erőforrásaiban|  Betekintést nyújt az előfizetésben lévő erőforrásokon végrehajtott műveletekbe.|    Rest API, [Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md)|
|[Azure Resource-naplók](../../azure-monitor/platform/platform-logs-overview.md)|Gyakori adatok az Azure Resource Manager-erőforrások előfizetésben való működéséről|   Betekintést nyújt az erőforrás által végrehajtott műveletekbe.| Azure Monitor|
|[Jelentéskészítés az Azure Active Directoryban](../../active-directory/reports-monitoring/overview-reports.md)|Naplók és jelentések | Felhasználói bejelentkezési tevékenységeket és rendszertevékenység-információkat jelent a felhasználókról és a csoportkezelésről.|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtuális gépek és felhőszolgáltatások](../../azure-monitor/learn/quick-collect-azurevm.md)|Windows eseménynapló szolgáltatás és Linux Syslog|  Rögzíti a rendszeradatokat és a naplózási adatokat a virtuális gépeken, és továbbítja az adatokat egy ön által kiválasztott tárfiókba.|   Windows (a Windows Azure Diagnostics [[WAD](../../monitoring-and-diagnostics/azure-diagnostics.md)] storage használatával) és Linux az Azure Monitorban|
|[Azure Storage-statisztika](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|A tárnaplózás mérőszámokat biztosít egy tárfiókhoz|Betekintést nyújt a nyomkövetési kérelmekbe, elemzi a használati trendeket, és diagnosztizálja a tárfiókkal kapcsolatos problémákat.|   REST API vagy az [ügyféltár](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Hálózati biztonsági csoport (NSG) folyamatnaplói](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON formátum, szabályonként mutatja a kimenő és bejövő folyamatokat|Információkat jelenít meg a hálózati biztonsági csoporton keresztüli be- és kimenő IP-forgalomról.|[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Alkalmazásbetekintés](../../azure-monitor/app/app-insights-overview.md)|Naplók, kivételek és egyéni diagnosztika|  Alkalmazásteljesítmény-figyelési (APM) szolgáltatást biztosít a webfejlesztők számára több platformon.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[Adatok / biztonsági riasztások feldolgozása](../../security-center/security-center-intro.md)|  Az Azure Security Center riasztásai, az Azure Monitor naplói riasztások|    Biztonsági információkat és riasztásokat tartalmaz.|  REST API-k, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Naplóintegráció helyszíni SIEM-rendszerekkel
[A Security Center-riasztások integrálása bemutatja,](../../security-center/security-center-export-data-to-siem.md) hogyan szinkronizálhatja a Security Center-riasztásokat, az Azure diagnosztikai naplók által gyűjtött virtuálisgép-biztonsági eseményeket, valamint az Azure naplózási naplóit az Azure Monitor-naplókkal vagy a SIEM-megoldással.

## <a name="next-steps"></a>További lépések

- [Naplózás és naplózás:](management-monitoring-overview.md)Az adatok védelme a láthatóság megőrzésével és az időben érkező biztonsági riasztásokra való gyors reagálással.

- [Biztonsági naplózás és naplózási napló gyűjtése az Azure-on belül:](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)Kényszerítse ezeket a beállításokat annak érdekében, hogy az Azure-példányok a megfelelő biztonsági és naplózási naplókat gyűjtsék.

- [Webhelycsoport naplózási beállításainak konfigurálása:](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)Ha webhelycsoport-rendszergazda, olvassa be az egyes felhasználók műveleteinek előzményeit és az adott dátumtartományban végrehajtott műveletek előzményeit.

- [Keressen a naplóban az Office 365 Biztonsági & megfelelőségi központban:](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)Az Office 365 Biztonsági & Megfelelőségi központban kereshet az egyesített naplóban, és megtekintheti az Office 365-szervezet felhasználói és rendszergazdai tevékenységét.
