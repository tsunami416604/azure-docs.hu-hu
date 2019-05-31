---
title: Az Azure monitorban adatforrásokat |} A Microsoft Docs
description: Ismerteti az adatok állapotát és teljesítményét az Azure-erőforrások és a rajtuk futó alkalmazások figyeléséhez másolatára.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357481"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Figyelési adatok az Azure Monitor forrásai
Az Azure Monitor alapul egy [gyakori figyelési adatplatform](data-platform.md) tartalmazó [naplók](data-platform-logs.md) és [metrikák](data-platform-metrics.md). Adatok gyűjtése a platform lehetővé teszi, hogy eszközök készletének használata az Azure monitorban elemezni különböző erőforrásokból származó adatokat. Monitorozási adatok is megkaphatja a más helyekre bizonyos forgatókönyvek támogatásához, és bizonyos erőforrások előfordulhat, hogy írási más helyekre, előtt összegyűjtött naplók és mérőszámok az is lehet.

Ez a cikk ismerteti a figyelési mellett a monitorozási adatok, az Azure-erőforrások létrehozása az Azure Monitor által gyűjtött adatok különböző forrásokból. Hivatkozások részletes információkat a különböző helyeken az adatok összegyűjtéséhez szükséges konfigurációs állnak rendelkezésre.

## <a name="application-tiers"></a>Alkalmazásrétegek

Figyelési adatok az Azure-alkalmazások forrásai rétegeket, a legmagasabb szinten folyamatban van az alkalmazás saját maga és az alacsonyabb rétegek folyamatban van az Azure platform összetevői csoportosíthatóak. A metódus az adathozzáférés az egyes szintekről változik. Az alkalmazásrétegek az alábbi táblázat foglalja össze, és a figyelési adatok az egyes szintek források jelennek meg az alábbi szakaszokban található. Lásd: [figyelési adatok helye az Azure-ban](data-locations.md) minden helyének és az adatok elérése leírását.


![Figyelési szint](../media/overview/overview.png)


### <a name="azure"></a>Azure
Az alábbi táblázat röviden ismerteti az alkalmazásrétegek konkrétan az Azure-bA. Az alábbi szakaszokban található egyes mutató hivatkozást követve további részletei.

| Szint | Leírás | Fizetési mód |
|:---|:---|:---|
| [Az Azure-bérlő](#azure-tenant) | A bérlői szintű Azure-szolgáltatások, például az Azure Active Directory művelettel kapcsolatos adatokat. | AAD-adatok megtekintése a portálon, vagy egy bérlői diagnosztikai beállítás használata az Azure Monitor gyűjtemény konfigurálja. |
| [Azure-előfizetés](#azure-subscription) | Egészségügyi és erőforrások közötti szolgáltatások például a Resource Manager és a szolgáltatások állapotát az Azure-előfizetés kezeléséhez kapcsolódó adatokat. | A portál megtekintése, vagy konfigurálhatja az Azure Monitor log-profillal való gyűjtését. |
| [Azure-erőforrások](#azure-resources) |  A művelet és az egyes Azure-erőforrások teljesítményét adatait. | Gyűjtött metrikák automatikusan, a Metrikaböngészőben megtekintése.<br>A naplók az Azure monitorban diagnosztikai beállításainak konfigurálása.<br>Figyelési megoldások és Insights részletesebb figyelés bizonyos erőforrástípusok esetében érhető el. |

### <a name="azure-other-cloud-or-on-premises"></a>Az Azure, más felhőalapú vagy helyszíni 
Az alábbi táblázat röviden ismerteti, amely lehet az Azure, egy másik felhőalapú vagy helyszíni alkalmazásrétegek. Az alábbi szakaszokban található egyes mutató hivatkozást követve további részletei.

| Szint | Leírás | Fizetési mód |
|:---|:---|:---|
| [Operációs rendszer (vendég)](#operating-system-guest) | Az operációs rendszert a számítási erőforrások adatait. | Ügyféladatforrások gyűjtése a Azure Monitor és a függőségi ügynököt támogatása az Azure Monitor-beli virtuális gépek függőségeit gyűjtése a Log Analytics-ügynök telepítése.<br>Azure-beli virtuális gépek telepítse az Azure diagnosztikai bővítmény gyűjtendő naplók és mérőszámok az Azure Monitor szolgáltatásba. |
| [Alkalmazáskód](#application-code) | A teljesítmény és a tényleges alkalmazás és a kódot, beleértve a teljesítmény-nyomkövetés, alkalmazásnaplókat és telemetriai felhasználói adatait. | A kód adatokat gyűjteni az Application insightsba való alkalmazásáról. |
| [Egyéni források](#custom-sources) | Külső szolgáltatások vagy más összetevők és eszközök adatait. | Napló vagy metrikák az Azure Monitor szolgáltatásba történő adatgyűjtést bármely REST-ügyféllel. |

## <a name="azure-tenant"></a>Azure-bérlő
Az Azure-bérlőhöz kapcsolódó telemetriai adatokat gyűjt a bérlői szintű szolgáltatások, például az Azure Active Directory.

![Az Azure-bérlő gyűjtemény](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Az Azure Active Directory-naplók
[Jelentéskészítés az Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) bejelentkezési tevékenység és a naplózási beállításainak egy adott bérlőn belül végrehajtott módosítások előzményeit tartalmazza. 

| Cél | Leírás | Leírások |
|:---|:---|:---|
| Azure Monitor naplóira | Állítsa be kell gyűjteni azokat más figyelési adatok elemzése az Azure Monitor, az Azure AD naplókat. | [Az Azure AD-naplók integrálása az Azure Monitor naplóira (előzetes verzió)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Az Azure AD-naplók exportálása az Azure Storage alacsony költségű. | [Oktatóanyag: Az Azure storage-fiókba (előzetes verzió) az Azure AD-naplóinak archiválása](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Eseményközpont | Az Azure AD Stream naplók más helyekre, Event Hubs használatával. | [Oktatóanyag: Az Azure event hubhoz (előzetes verzió) az Azure Active Directory-naplók Stream](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure-előfizetés
Egészségügyi és működését az Azure-előfizetéshez kapcsolódó telemetria.

![Azure-előfizetés](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-tevékenységnapló 
A [Azure tevékenységnapló](activity-logs-overview.md) tartalmazza a service health rekordok konfiguráció módosításai az erőforrásokat az Azure-előfizetésében lévő rekordok együtt. A tevékenységnapló érhető el az összes Azure-erőforrások és jelöli a _külső_ megtekintése.

| Cél | Leírás | Leírások |
|:---|:---|
| Tevékenységnapló | A tevékenységnapló gyűjti be a saját adattár, amely az Azure Monitor menüben megtekintheti vagy a tevékenységnapló-riasztások létrehozásához használja. | [Lekérdezés a tevékenységnapló az Azure Portalon](activity-log-view.md#azure-portal) |
| Azure Monitor naplóira | Az Azure Monitor naplóira a tevékenységnapló elemezheti azt más figyelési adatok gyűjtéséhez konfigurálja. | [Összegyűjtheti és elemezheti a Log Analytics-munkaterületet az Azure monitorban az Azure-Tevékenységnaplók](activity-log-collect.md) |
| Azure Storage | A tevékenységnapló exportálása az Azure Storage alacsony költségű. | [Tevékenységnapló archiválása](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Stream a tevékenységnapló más helyekre, Event Hubs használatával | [Stream tevékenység naplózás az Eseményközpontba](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Service Health
[Az Azure Service Health](../../service-health/service-health-overview.md) az Azure-szolgáltatások az előfizetésében, amelyet az alkalmazás és-erőforrások állapotával kapcsolatos információkat nyújt.

| Cél | Leírás | Leírások |
|:---|:---|:---|
| Tevékenységnapló<br>Azure Monitor naplóira | A Service Health tárolja a rekordokat az Azure tevékenységnaplójának, így megtekintheti őket az Azure Portalon, vagy bármely más hajthat végre a tevékenységnaplóban a tevékenységek végrehajtására. | [Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon](service-notifications.md) |


## <a name="azure-resources"></a>Azure-erőforrások
Metrikák és erőforrás szintű diagnosztikai naplók kapcsolatos adatok megadása a _belső_ az Azure-erőforrás a művelet. A legtöbb Azure-szolgáltatásokhoz érhető el, és figyelési megoldások és az elemzések gyűjtése adatkereten felül adott szolgáltatásokhoz.

![Az Azure erőforrás-gyűjtemény](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Platform-metrikák 
Küldi a legtöbb Azure-szolgáltatás [platform metrikák](data-platform-metrics.md) azok teljesítményére és metrikák adatbázissal művelet tükrözik. Az adott [metrikák változnak az olyan erőforrástípusok](metrics-supported.md). 

| Cél | Leírás | Leírások |
|:---|:---|:---|
| Az Azure Monitor-metrikák | Platform metrikák fogja írni az Azure Monitor-metrikák adatbázis konfiguráció nélkül. Metrikaböngésző hozzáférés platform metrikáit.  | [Ismerkedés az Azure Metrikaböngésző](metrics-getting-started.md)<br>[Az Azure monitorban támogatott mérőszámok](metrics-supported.md) |
| Azure Monitor naplóira | Másolja be platform metrikák népszerű és más elemzése a Log Analytics használatával a naplókhoz. | [A Log Analyticshez való közvetlen az Azure diagnostics](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| Event Hubs | Stream-metrikák más helyekre, Event Hubs használatával. |[Stream Azure monitorozási adatok felhasználásra egy eseményközpontba egy külső eszközzel](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>Diagnosztikai naplók
[Diagnosztikai naplók](diagnostic-logs-overview.md) betekintést nyújtson az _belső_ egy Azure-erőforrás a művelet.  Diagnosztikai naplók alapértelmezés szerint nem engedélyezettek. Kell engedélyezheti őket, és adja meg az egyes erőforrások helyét. 

A konfigurációs követelmények és a diagnosztikai naplók tartalmának erőforrástípus szerint eltérőek lehetnek, és nem minden szolgáltatás még a diagnosztikai naplók létrehozása. Lásd: [támogatott szolgáltatások, sémákat és kategóriák az Azure diagnosztikai naplók](diagnostic-logs-schema.md) minden szolgáltatás és a részletes konfigurációs hivatkozásokat tartalmaz. Ha a szolgáltatás nem szerepel ebben a cikkben, majd a szolgáltatás jelenleg nem írni a diagnosztikai naplók.

| Cél | Leírás | Leírások |
|:---|:---|:---|
| Azure Monitor naplóira | Diagnosztikai naplók küldése az Azure Monitor naplóira egyéb összegyűjtött napló adatokat az elemzéshez. Bizonyos erőforrások közvetlenül az Azure Monitor írhat, míg mások a tárfiókhoz írása előtt a Log Analytics-munkaterület-bA importálja. | [Stream Azure diagnosztikai naplók a Log Analytics-munkaterületet az Azure monitorban](diagnostic-logs-stream-log-store.md)<br>[Naplók gyűjtése az Azure Storage-ból az Azure portal használatával](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| Storage | Diagnosztika küldése az Azure Storage-naplók archiváláshoz. | [Az Azure diagnosztikai naplóinak archiválása](archive-diagnostic-logs.md) |
| Event Hubs | Stream-diagnosztikai naplók az Event Hubs segítségével más helyekre. |[Stream Azure diagnosztikai naplók egy eseményközpontba](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Operációs rendszer (vendég)
Az Azure-ban, az egyéb felhőkben, és a helyszíni számítási erőforrások figyeléséhez vendég operációs rendszer rendelkezik. Egy vagy több ügynökök telepítésére a figyelési eszközök az Azure-szolgáltatások magukat, elemezheti azt az Azure Monitor az gyűjthet telemetriai adatokat a vendégről származó.

![Azure-beli számítási erőforrás-gyűjtemény](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Az Azure diagnosztikai bővítmény
Az Azure diagnosztikai bővítmény engedélyezése az Azure-beli virtuális gépek lehetővé teszi a gyűjtendő naplók és mérőszámok az Azure vendég operációs rendszerből a számítási erőforrásokat, beleértve az Azure-Felhőszolgáltatás (klasszikus) webes és feldolgozói szerepkörök, virtuális gépek, virtuális gép méretezési csoportokat, és a Service Fabric.

| Cél | Leírás | Leírások |
|:---|:---|:---|
| Storage | A diagnosztikai bővítmény engedélyezésekor, alapértelmezés szerint írni fogja egy storage-fiókot. | [Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](diagnostics-extension-to-storage.md) |
| Az Azure Monitor-metrikák | A diagnosztikai bővítményt a teljesítményszámlálók adatainak összegyűjtése konfigurálásakor az Azure Monitor-metrikák adatbázis írja azokat. | [Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolja a Windows virtuális gép Resource Manager-sablon használatával](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights-Logs | Eseménynaplókat és teljesítményszámlálókat gyűjteni az alkalmazás többi alkalmazásadat elemezni támogató számítási erőforrásokat. | [Cloud Service, a virtuális gép vagy a Service Fabric diagnosztikai adatok küldése az Application Insights](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Állítsa be a diagnosztikai bővítmény más helyekre, Event Hubs használatával az adatok továbbításához.  | [A gyakori elérésű útvonal az Azure Diagnostics-adatok streamelés az Event Hubs használatával](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics-ügynököket 
Telepítse a Log Analytics-ügynök átfogó figyelési és a Windows vagy Linux rendszerű virtuális gépek felügyeletét. A virtuális gép az Azure, egy másik felhőalapú vagy helyszíni futtathat.

| Cél | Leírás | Leírások |
|:---|:---|:---|
| Azure Monitor naplóira | A Log Analytics-ügynököket kapcsolódik az Azure-ba közvetlenül vagy a System Center Operations Manager figyelése, és lehetővé teszi, hogy adatokat gyűjteni az adatforrásokat, konfigurálhat vagy az alkalmazások további betekintést biztosító megoldások monitorozása a virtuális gépen fut. | [Az ügynök adatforrások az Azure monitorban](agent-data-sources.md)<br>[Az Operations Manager csatlakoztatása az Azure monitornak](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez 
[A virtuális gépek az Azure Monitor](../insights/vminsights-overview.md) figyelési testreszabott élményt nyújt a virtuális gépek biztosítása az alapvető Azure Monitor-funkciók, beleértve a szolgáltatás állapotának és a virtuális gép állapota csomagban. A függőségi ügynököt Windows és Linux rendszerű virtuális gépeken, amely integrálható a virtuális gép és a külső folyamat függőségek futó folyamatok detektált adatok gyűjtése a Log Analytics-ügynök van szükség.

| Cél | Leírás | Leírások |
|:---|:---|:---|
| Azure Monitor naplóira | Folyamatok és a függőségek adatait tárolja az ügynökön. | [Az Azure Monitor használatával virtuális gépek (előzetes verzió) térkép az alkalmazás-összetevők ismertetése](../insights/vminsights-maps.md) |
| Virtuálisgép-tároló | A virtuális gépek az Azure Monitor egy egyéni helyre heath állapotadatait tárolja. Ez a lehetőség csak az Azure monitornak a virtuális gépek mellett az Azure Portalon a [REST API-t az Azure Resource health](/rest/api/resourcehealth/). | [Az Azure-beli virtuális gépek állapotának ismertetése](../insights/vminsights-health.md)<br>[Az Azure Resource health REST API-val](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Alkalmazáskód
Részletes alkalmazás figyelése az Azure Monitor használatával történik [Application Insights](https://docs.microsoft.com/azure/application-insights/) amely gyűjti az adatokat számos platformon futó alkalmazások. Az alkalmazás az Azure, egy másik felhőalapú vagy helyszíni futtathat.

![Alkalmazás adatgyűjtésének](media/data-sources/applications.png)


### <a name="application-data"></a>Alkalmazásadatok
Amikor engedélyezi az Application Insights egy alkalmazás számára egy kialakítási csomag telepítése, gyűjti, metrikák és naplók a teljesítménnyel és az alkalmazás működésének kapcsolatos. Az Application Insights összegyűjti az adatokat más adatforrásokkal által használt azonos az Azure Monitor adatplatform tárolja. Adatok elemzésére szolgáló kiterjedt eszközöket tartalmaz, de az eszközök, például a Metrikaböngésző és a Log Analytics használatával más forrásokból származó adatokat is elemezheti.

| Cél | Leírás | Leírások |
|:---|:---|:---|
| Azure Monitor naplóira | Az alkalmazás, például a lapmegtekintések, alkalmazásokra irányuló kérések, kivételek és nyomok működési adatait. | [Elemezheti a naplófájlok adatait az Azure monitorban](../log-query/log-query-overview.md) |
|                    | Függőségi információk alkalmazás-hozzárendelés támogatása az alkalmazás-összetevők és telemetriai korreláció között. | [Az Application Insights telemetriai korreláció](../app/correlation.md) <br> [Alkalmazástérkép](../app/app-map.md) |
|            | A rendelkezésre állásának és válaszkészségének az alkalmazás különböző helyekről, a nyilvános interneten tesztelését rendelkezésre állási tesztek eredményei. | [Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése](../app/monitor-web-app-availability.md) |
| Az Azure Monitor-metrikák | Az Application Insights a teljesítmény és a művelet az alkalmazás mellett egyéni metrikákat, amelyeket az alkalmazás az Azure Monitor-metrikák adatbázisba leíró metrikákat gyűjt. | [Napló-alapú és előre összesített mérőszámok az Application insights szolgáltatásban](../app/pre-aggregated-metrics-log-metrics.md)<br>[Application Insights API egyéni eseményekhez és metrikák](../app/api-custom-events-metrics.md) |
| Azure Storage | Azure Storage alacsony költségű alkalmazás adatokat küldeni. | [Telemetria exportálása az Application Insightsból](../app/export-telemetry.md) |
|            | Rendelkezésre állási tesztek részleteit az Azure Storage szolgáltatásban tárolódnak. Az Azure Portalon az Application Insights használatával töltse le a helyi analysis. Rendelkezésre állási tesztek eredményét az Azure Monitor naplóira vannak tárolva. | [Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése](../app/monitor-web-app-availability.md) |
|            | Profiler nyomkövetési adatokat az Azure Storage szolgáltatásban tárolt. Az Azure Portalon az Application Insights használatával töltse le a helyi analysis.  | [Profil éles üzemi alkalmazások az Azure-ban az Application insights segítségével](../app/profiler-overview.md) 
|            | Hibakeresési pillanatkép, az Azure Storage szolgáltatásban tárolt kivételek részéhez rögzített adatokat. Az Azure Portalon az Application Insights használatával töltse le a helyi analysis.  | [A pillanatképek működése](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Figyelési megoldások és elemzések
[Figyelési megoldások](../insights/solutions.md) és [Insights](../insights/insights-overview.md) gyűjthet adatokat, amelyek egy adott szolgáltatás vagy alkalmazás működését további betekintést nyújtanak. Különböző alkalmazásrétegek és még több rétegre erőforrásaihoz, előfordulhat, hogy cím.

### <a name="monitoring-solutions"></a>Monitorozási megoldások

| Cél | Leírás | Leírások
|:---|:---|:---|
| Azure Monitor naplóira | Figyelési megoldások összegyűjtötte az adatokat az Azure Monitor naplóira hol lehet a lekérdezési nyelv segítségével elemezheti vagy [nézetek](view-designer.md) , amelyek általában bekerülnek a megoldásban. | [Adatok gyűjtése részletei figyelési megoldások az Azure-ban](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Az Azure Monitor for containers szolgáltatásban
[A tárolók az Azure Monitor](../insights/container-insights-overview.md) biztosít egy testre szabott figyelési élmény a [Azure Kubernetes Service (AKS)](/azure/aks/). Ez további adatokat gyűjti össze a következő táblázat ismerteti ezeket az erőforrásokat.

| Cél | Leírás | Leírások |
|:---|:---|:---|
| Azure Monitor naplóira | A tároló monitorozási adatok, beleértve a készlet, naplók és események AKS-hez. Metrikaadatok is annak érdekében, hogy a portálon az elemzési funkciói kihasználhatók naplók találhatók. | [AKS-fürt teljesítményvizsgálata tárolókhoz készült Azure Monitor segítségével](../insights/container-insights-analyze.md) |
| Az Azure Monitor-metrikák | Metrikaadatok meghajtó Vizualizáció és riasztások, a metrika adatbázisban tárolódik. | [Tároló mérőszámainak megtekintése a metrikaböngészőben](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Közel valós idejű szolgáltatássá sorrendben-tárolókhoz az Azure Monitor megadja az adatok közvetlenül az Azure Kubernetes service az Azure Portalon. | [Tárolónaplók valós idejű megtekintése a tárolókhoz készült Azure Monitorral (előzetes verzió)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
[A virtuális gépek az Azure Monitor](../insights/vminsights-overview.md) testreszabott élményt nyújt a virtuális gépek monitorozását. A virtuális gépek az Azure Monitor által összegyűjtött adatokat leírása megtalálható a [operációs rendszer (vendég)](#operating-system-guest) című fenti szakaszban.

## <a name="custom-sources"></a>Egyéni források
A standard csomagokhoz-alkalmazások, kívül szükség lehet egyéb telemetriai adatokból, hogy nem lehet összegyűjteni a más adatforrásokkal rendelkező erőforrások figyeléséhez. Ezeket az erőforrásokat, az adatokat írni az e metrikákat vagy naplókat az Azure Monitor API-val.

![Egyéni gyűjtéshez.](media/data-sources/custom.png)

| Cél | Módszer | Leírás | Leírások |
|:---|:---|:---|:---|
| Azure Monitor naplóira | Data Collector API | Naplózási adatokat gyűjt bármely REST-ügyféllel, és a Log Analytics-munkaterületen tárolja. | [Napló adatokat küldeni a HTTP-adatgyűjtő API (nyilvános előzetes verzió) az Azure Monitor](data-collector-api.md) |
| Az Azure Monitor-metrikák | Egyéni metrikák API | Bármilyen REST-ügyfél és a tároló az Azure Monitor-metrikák adatbázis metrikaadatok gyűjteni. | [Egyéni metrikák az Azure-beli erőforráshoz küldeni az Azure Monitor metrika tároló egy REST API-val](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Egyéb szolgáltatások
Más Azure-szolgáltatások adatokat írni az Azure Monitor data platform. Ez lehetővé teszi, hogy az Azure Monitor által gyűjtött adatokat ezek a szolgáltatások által gyűjtött adatok elemzése és azonos elemzési és vizualizációs eszközök.

| Szolgáltatás | Cél | Leírás | Leírások |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Azure Monitor naplóira | Az Azure Security Center összegyűjti a Log Analytics-munkaterület, amely lehetővé teszi, hogy az Azure Monitor által gyűjtött egyéb log adatokkal elemezheti a biztonsági adatait tárolja.  | [Adatgyűjtés az Azure Security Centerben](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor naplóira | Az Azure Sentinel gyűjti a Log Analytics-munkaterület, amely lehetővé teszi, hogy más Azure-figyelése által gyűjtött naplóadatok elemezni a különböző forrásokból származó adatokat tárolja.  | [Csatlakozás adatforrásokhoz](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [típusú monitorozási adatait az Azure Monitor által gyűjtött](data-platform.md) , és hogyan tekintheti meg és elemezheti ezeket az adatokat.
- Lista a [adatok tárolására az Azure-erőforrások különböző helyeken](data-locations.md) , és hogy hozzá tud férni. 
