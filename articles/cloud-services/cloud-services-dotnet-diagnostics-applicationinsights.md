---
title: "Hibaelhárítás az Application Insights segítségével Felhőszolgáltatások |} Microsoft Docs"
description: "Ismerje meg az Application Insights segítségével folyamat adataihoz az Azure Diagnostics cloud service kapcsolatos problémák elhárítása."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Hibaelhárítás az Application Insights segítségével Cloud Services
A [Azure SDK 2.8](https://azure.microsoft.com/downloads/) és az Azure diagnostics-bővítmény 1.5-ös küldhet Azure diagnosztikai adatokat a felhőalapú szolgáltatás közvetlenül az Application Insights részére. A naplók Azure Diagnostics által gyűjtött&mdash;többek között az alkalmazásnaplók, a Windows eseménynaplóiban keresse meg, az ETW-naplók és a teljesítményszámlálók&mdash;Application Insights lehet küldeni. Majd jelenítheti meg az Application Insights portál felhasználói felületének ezt az információt. Az Application Insights SDK segítségével majd metrikák és az alkalmazás, valamint a rendszer- és infrastruktúra-szintű származó Azure Diagnostics érkező naplók beolvasása.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Azure diagnosztikai adatokat küldeni a Application Insights konfigurálása
Kövesse az alábbi lépéseket a felhőszolgáltatás-projekt beállítása az Azure diagnosztikai adatokat küldeni a Application insights szolgáltatással.

1. A Visual Studio Solution Explorerben kattintson a jobb gombbal egy szerepkört, és válassza ki **tulajdonságok** a szerepkör-tervező megnyitása.

    ![Solution Explorer szerepkör tulajdonságai][1]

2. Az a **diagnosztika** szakasz a szerepkör Designer, jelölje be a **diagnosztikai adatok küldése az Application Insights** lehetőséget.

    ![Szerepkör designer diagnosztikai adatok küldése az application insights][2]

3. Előugró párbeszédpanelen válassza ki az Application Insights-erőforrást, amelyet szeretne küldeni az Azure diagnosztikai adatokat. A párbeszédpanel lehetővé teszi, hogy, hogy válassza ki egy meglévő Application Insights-erőforrást az előfizetésből, vagy adja meg manuálisan az Application Insights-erőforrás instrumentation kulcsa. Application Insights-erőforrás létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy új Application Insights-erőforrást](../application-insights/app-insights-create-new-resource.md).

    ![Válassza ki az application insights-erőforrás][3]

    Miután hozzáadta az Application Insights-erőforrást, a instrumentation kulcsot, az adott erőforrás tárolja a nevű szolgáltatás konfigurációs beállításként **APPINSIGHTS_INSTRUMENTATIONKEY**. A konfigurációs beállítást, az egyes szolgáltatáskonfiguráció vagy a környezet módosíthatja. Ehhez az szükséges, válassza ki a különböző konfigurációt a **szolgáltatáskonfiguráció** listában, és adja meg, hogy a konfiguráció új instrumentation kulcsot.

    ![Válassza ki a szolgáltatás konfigurációja][4]

    A **APPINSIGHTS_INSTRUMENTATIONKEY** konfigurációs beállítás segítségével a Visual Studio Application Insights-erőforrás információkkal közzététele során a diagnosztika-kiterjesztés konfigurálása. A konfigurációs beállítás kényelmes módja a meghatározásának különböző szolgáltatáskonfiguráció különböző instrumentation kulcsokat. Visual Studio fog beállítás fordítása, és helyezze be a diagnosztika bővítmény nyilvános konfigurációjának a közzététel során. A diagnosztika bővítmény konfigurálása a PowerShell segítségével egyszerűbbé, hogy csomag kimenetét a Visual Studio Application Insights instrumentation megfelelő kulccsal rendelkező nyilvános konfigurációs XML-t is tartalmaz. A nyilvános konfigurációs fájlok jönnek létre a bővítmények mappában, és kövesse a minta *PaaSDiagnostics.&lt; RoleName&gt;. PubConfig.xml*. A PowerShell-alapú telepítések ebben a mintában segítségével minden egyes konfigurációs hozzárendelése egy szerepkörhöz.

4) Az összes teljesítményszámlálók és az Application Insights az Azure diagnostics ügynök által gyűjtött hiba szintű naplókat küldeni az Azure diagnostics megadásához engedélyezze a **diagnosztikai adatok küldése az Application Insights** lehetőséget. 

    Ha azt szeretné, hogy mely adatokról további konfigurálásához az Application insights szolgáltatásnak elküldött, manuálisan módosítania kell a *diagnostics.wadcfgx* fájl az egyes szerepkörökhöz. Lásd: [konfigurálása Azure diagnosztikai adatokat küldeni a Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights) tudhat meg többet az beállításainak manuális frissítése.

Ha a felhőszolgáltatás Azure diagnosztikai adatok küldése az application insights van konfigurálva, ezután telepítheti azt az Azure-bA általában meggyőződött arról, hogy az Azure diagnostics bővítmény pedig engedélyezve legyen. További információkért lásd: [közzététele a felhőalapú szolgáltatás, a Visual Studio használatával](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Az Azure diagnosztikai adatainak megtekintése az Application Insights
Az Azure diagnosztikai telemetriai megjelennek a felhőalapú szolgáltatás számára megadott Application Insights-erőforrást.

Az Azure diagnostics napló típusok leképezés Application insights szolgáltatással kapcsolatos fogalmak, az alábbi módszerek:

* Teljesítményszámlálók, egyéni metrikákat az Application Insightsban jelennek meg.
* Windows eseménynaplóiban keresse meg a nyomkövetési adatokat és az Application Insightsban egyéni események jelennek meg.
* Alkalmazás naplóit, ETW naplók és a diagnosztika infrastruktúra naplók jelennek meg az Application Insights nyomkövetési adatokat.

Az Application Insights Azure diagnosztikai adatainak megtekintéséhez tegye a következők egyikét:

* Használjon [metrikaböngésző](../application-insights/app-insights-metrics-explorer.md) bármilyen egyéni teljesítményszámlálóit vagy számát is különböző típusú Windows-Eseménynapló eseményeinek megjelenítéséhez.

    ![Egyéni metrikák a Metrikaböngészőben][5]

* Használjon [keresési](../application-insights/app-insights-diagnostic-search.md) között az Azure Diagnostics által küldött nyomkövetési napló keresése. Például, ha a nem kezelt kivételt okozott összeomlás, és hasznosítsa újra a szerepkör, információ a kivételről megjelennek a *alkalmazás* csatornája *Windows-Eseménynapló*. Használhatja a Keresés a teljes Veremkivonat a kivételhez megtalálásához a probléma okát, és nézze meg a Windows eseménynaplóban.

    ![Keresési nyomkövetések][6]

## <a name="next-steps"></a>Következő lépések
* [Az Application Insights SDK hozzáadása a felhőalapú szolgáltatás](../application-insights/app-insights-cloudservices.md) kérelmek, kivételek, függőségeit és bármilyen egyéni telemetriai adatokat küldeni az alkalmazásból. Az Azure diagnosztikai adatokat együtt, ezt az információt kaphat a teljes rálátást az alkalmazás és a rendszer, mind az azonos Application Insights-erőforrás.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
