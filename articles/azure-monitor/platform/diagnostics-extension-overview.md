---
title: Az Azure Diagnostics bővítmény áttekintése
description: Azure-diagnosztika használata hibakereséshez, teljesítményméréshez, figyeléshez, forgalomelemzéshez a felhőszolgáltatásokban, a virtuális gépekben és a szolgáltatáshálóban
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672378"
---
# <a name="azure-diagnostics-extension-overview"></a>Az Azure Diagnostics bővítmény áttekintése
Az Azure Diagnostics bővítmény az Azure Monitor egy [ügynöke,](agents-overview.md) amely az Azure számítási erőforrások vendég operációs rendszeréből, beleértve a virtuális gépeket is, figyelési adatokat gyűjt. Ez a cikk áttekintést nyújt az Azure Diagnostics bővítményről, beleértve az általa támogatott funkciókat és a telepítési és konfigurációs lehetőségeket. 

> [!NOTE]
> Az Azure Diagnostics bővítmény az egyik olyan ügynök, amely a számítási erőforrások vendég operációs rendszeréből figyelési adatokat gyűjthet. Tekintse [meg az Azure Monitor-ügynökök áttekintése](agents-overview.md) a különböző ügynökök leírását és útmutatást a megfelelő ügynökök kiválasztásához a követelményeknek.

## <a name="comparison-to-log-analytics-agent"></a>Összehasonlítás a Log Analytics-ügynökkel
Az Azure Monitor Log Analytics-ügynöke is használható figyelési adatok gyűjtésére a virtuális gépek vendég operációs rendszeréből. Az igényeitől függően választhatja az egyik vagy mindkettő használatát. Az Azure Monitor-ügynökök részletes összehasonlítása [az Azure Monitor-ügynökök áttekintése című témakörben olvashat.](agents-overview.md) 

A legfontosabb figyelembe vemandó különbségek a következők:

- Az Azure Diagnostics Extension csak azure-beli virtuális gépekkel használható. A Log Analytics-ügynök az Azure-ban, más felhőkben és a helyszíni virtuális gépekkel is használható.
- Az Azure Diagnostics bővítmény adatokat küld az Azure Storage, [az Azure Monitor metrikák](data-platform-metrics.md) (csak Windows) és az Event Hubs. A Log Analytics-ügynök adatokat gyűjt az [Azure Monitor naplók](data-platform-logs.md).
- A Log Analytics-ügynök szükséges [a megoldásokhoz](../monitor-reference.md#insights-and-core-solutions), az [Azure Monitor virtuális gépekhez](../insights/vminsights-overview.md)és más szolgáltatásokhoz, például az [Azure Security Centerhez.](/azure/security-center/)

## <a name="costs"></a>Költségek
Az Azure diagnosztikai bővítmény nem jár költséggel, de a bevitt adatokért díjat kell fizetnie. Ellenőrizze [az Azure Monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/) arra a célra vonatkozóan, ahol adatokat gyűjt.

## <a name="data-collected"></a>Összegyűjtött adatok
Az alábbi táblázatok a Windows és a Linux diagnosztikai bővítmény által gyűjthető adatokat sorolják fel.

### <a name="windows-diagnostics-extension-wad"></a>Windows diagnosztikai bővítmény (WAD)

| Adatforrás | Leírás |
| --- | --- |
| Windows eseménynaplók   | Események a Windows eseménynaplójából. |
| Teljesítményszámlálók | Az operációs rendszer és a munkaterhelések különböző aspektusainak teljesítményét mérő numerikus értékek. |
| IIS-naplók             | A vendég operációs rendszeren futó IIS-webhelyek használati adatai. |
| Alkalmazásnaplók     | Az alkalmazás által írt üzenetek nyomon követése. |
| .NET EventSource-naplók |Kódírási események a .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) osztály használatával |
| [Manifest alapú ETW naplók](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Eseménykövetés bármely folyamat által létrehozott Windows-eseményekhez. |
| Összeomlási memóriaképek (naplók)   | Információ a folyamat állapotáról, ha egy alkalmazás összeomlik. |
| Fájlalapú naplók    | Az alkalmazás vagy szolgáltatás által létrehozott naplók. |
| Ügynökdiagnosztikai naplók | Információk magáról az Azure Diagnostics. |


### <a name="linux-diagnostics-extension-lad"></a>Linux diagnosztikai bővítmény (LAD)

| Adatforrás | Leírás |
| --- | --- |
| Rendszernapló | A Linux eseménynaplózási rendszerbe küldött események.   |
| Teljesítményszámlálók  | Az operációs rendszer és a munkaterhelések különböző aspektusainak teljesítményét mérő numerikus értékek. |
| Naplófájlok | Fájlalapú naplóba küldött bejegyzések.  |

## <a name="data-destinations"></a>Adatcélok
Az Azure Diagnostic bővítmény windowsos és Linux os rendszerhez mindig adatokat gyűjt egy Azure Storage-fiókba. Lásd: Telepítse és konfigurálja a [Windows Azure diagnosztikai bővítmény (WAD)](diagnostics-extension-windows-install.md) és a Linux diagnosztikai bővítmény segítségével a [metrikák és naplók figyelése](../../virtual-machines/extensions/diagnostics-linux.md) az adott táblák és blobok listáját, ahol ezeket az adatokat gyűjtik.

Konfiguráljon egy vagy több *adatgyűjtőt* úgy, hogy adatokat küldjön más további célhelyekre. A következő szakaszok a Windows és linux os diagnosztikai bővítményhez elérhető fogadók at sorolják fel.

### <a name="windows-diagnostics-extension-wad"></a>Windows diagnosztikai bővítmény (WAD)

| Cél | Leírás |
|:---|:---|
| Az Azure Monitor metrikák | Teljesítményadatok gyűjtése az Azure Monitor metrikák. Lásd: [Vendég operációsrendszer-metrikák küldése az Azure Monitor metrika-adatbázisába.](collect-custom-metrics-guestos-resource-manager-vm.md)  |
| Event Hubs | Az Azure Event Hubs használatával adatokat küldhet az Azure-on kívülre. Lásd: [Az Azure diagnosztikai adatok streamelése az eseményközpontokba](diagnostics-extension-stream-event-hubs.md) |
| Azure Storage-blobok | A táblák mellett adatokat is írhat az Azure Storage-ban lévő blobokba. |
| Application Insights | Adatokat gyűjthet a virtuális gépen futó alkalmazásokból az Application Insightsba, hogy integrálódjon más alkalmazásfigyelésekkel. Lásd: [Diagnosztikai adatok küldése az Application Insights ba](diagnostics-extension-to-application-insights.md). |

A TÁROLÁSI ADATOKAT a Storage-ból egy Log Analytics-munkaterületről is gyűjtheti, hogy elemezze azokat az Azure Monitor naplókkal, bár a Log Analytics-ügynök általában ehhez a funkcióhoz használatos. Az adatokat közvetlenül a Log Analytics-munkaterületre küldheti, és támogatja a további funkciókat biztosító megoldásokat és elemzéseket.  Lásd: [Azure diagnosztikai naplók gyűjtése az Azure Storage-ból.](diagnostics-extension-logs.md) 


### <a name="linux-diagnostics-extension-lad"></a>Linux diagnosztikai bővítmény (LAD)
A LAD adatokat ír az Azure Storage tábláiba. Támogatja a következő táblázatban található mosogatókat.

| Cél | Leírás |
|:---|:---|
| Event Hubs | Az Azure Event Hubs használatával adatokat küldhet az Azure-on kívülre. |
| Azure Storage-blobok | A táblák mellett adatokat is írhat az Azure Storage-ban lévő blobokba. |
| Az Azure Monitor metrikák | Telepítse a Telegraf ügynök mellett LAD. Lásd: [Egyéni metrikák gyűjtése egy Linux virtuális géphez az InfluxData Telegraf ügynökkel.](collect-custom-metrics-linux-telegraf.md)


## <a name="installation-and-configuration"></a>Telepítés és konfigurálás
A diagnosztikai bővítmény az Azure-ban [virtuálisgép-bővítményként](../../virtual-machines/extensions/overview.md) van megvalósítva, így ugyanazokat a telepítési beállításokat támogatja az Erőforrás-kezelő sablonok, a PowerShell és a CLI használatával. A virtuálisgép-bővítmények telepítésével és karbantartásával kapcsolatos általános részleteket a [Virtuálisgép-bővítmények](../../virtual-machines/extensions/features-windows.md) telepítésével és karbantartásával kapcsolatos általános részleteka Windows- és [virtuálisgép-bővítmények hez és linuxos szolgáltatásokhoz](../../virtual-machines/extensions/features-linux.md) című témakörben találja.

A Windows és a Linux diagnosztikai bővítményt is telepítheti és konfigurálhatja az Azure Portalon a **diagnosztikai beállítások** csoportban a virtuális gép menüjének **Figyelés** szakaszában.

A diagnosztikai bővítmény Windows és Linux esetén történő telepítésével és konfigurálásával kapcsolatos további részleteket az alábbi cikkekben találja.

- [A Windows Azure diagnosztikai bővítmény (WAD) telepítése és konfigurálása](diagnostics-extension-windows-install.md)
- [Metrikák és naplók figyelése a Linux diagnosztikai bővítmény használatával](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Egyéb dokumentáció

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure Cloud Service (klasszikus) webes és feldolgozói szerepkörök
- [Bevezetés a felhőszolgáltatás figyelésének bevezetésébe](../../cloud-services/cloud-services-how-to-monitor.md)
- [Az Azure-diagnosztika engedélyezése az Azure Felhőszolgáltatásokban](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights for Azure felhőszolgáltatásokhoz](../app/cloudservices.md)<br>[Felhőalapú szolgáltatások alkalmazás folyamatának nyomon követése az Azure Diagnostics segítségével](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Szolgáltatások monitorozása és diagnosztizálása egy helyi gép fejlesztési beállításánál](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>További lépések


* Ismerje meg [a teljesítményszámlálók használatát az Azure Diagnostics szolgáltatásban.](../../cloud-services/diagnostics-performance-counters.md)
* Ha problémája van a diagnosztika indításával vagy az adatok keresésével az Azure storage-táblázatokban, olvassa el a [TroubleShooting Azure Diagnostics című témakört.](diagnostics-extension-troubleshooting.md)

