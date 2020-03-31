---
title: Az Azure figyelési adatainak streamelése az eseményközpontba
description: Ismerje meg, hogyan streamelheti az Azure figyelési adatait egy eseményközpontba, hogy az adatokat egy partner SIEM vagy elemzési eszközbe tudja bejuttatni.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 08177165439ff7d3205e31757e5d1e28759a9836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274189"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Azure monitorozási adatok streamelése egy eseményközpontba
Az Azure Monitor teljes körű figyelési megoldást kínál az Azure-beli alkalmazások és szolgáltatások számára, más felhőkben és a helyszínen. Amellett, hogy az Azure Monitor használatával elemzi az adatokat, és kihasználja azokat a különböző figyelési forgatókönyvek, előfordulhat, hogy el kell küldenie a környezetmás figyelési eszközök. A legtöbb esetben a figyelési adatok külső eszközökre történő streamelésének leghatékonyabb módja az [Azure Event Hubs](/azure/event-hubs/)használata. Ez a cikk rövid leírást ad arról, hogyan streamelheti a figyelési adatokat a különböző forrásokból egy eseményközpontba, és részletes útmutatásra mutató hivatkozásokat.


## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Mielőtt bármilyen adatforráshoz konfigurálna streamelést, létre kell [hoznia egy Event Hubs névteret és eseményközpontot.](../../event-hubs/event-hubs-create.md) Ez a névtér és az eseményközpont az összes figyelési adat célja. Az Event Hubs-névtér az azonos hozzáférési szabályzattal rendelkező eseményközpontok logikai csoportosítása, hasonlóan ahhoz, hogy egy tárfiók egyedi blobokkal rendelkezik az adott tárfiókon belül. Vegye figyelembe az alábbi részleteket az eseményközpontok névteréről és az eseményközpontokról, amelyeket a figyelési adatok streameléséhez használ:

* Az átviteli egységek száma lehetővé teszi az eseményközpontok átviteli skálájának növelését. Általában csak egy átviteli egységre van szükség. Ha a naplóhasználat növekedésével növekvő léptéket kell felskáláznia, manuálisan növelheti a névtér átviteli egységeinek számát, vagy engedélyezheti az automatikus felfújást.
* A partíciók száma lehetővé teszi, hogy párhuzamosítsa a felhasználás t sok fogyasztó között. Egyetlen partíció legfeljebb 20 Mb/s vagy másodpercenként körülbelül 20 000 üzenetet támogathat. Az adatokat használó eszköztől függően előfordulhat, hogy több partícióról is támogatja a fogyasztást. Négy partíció ésszerű kezdeni, ha nem biztos abban, ha nem biztos a partíciók számát.
* Az eseményközpontban legalább 7 napra állítja be az üzenetmegőrzést. Ha a fogyasztó eszköz lemegy több mint egy nap, ez biztosítja, hogy az eszköz is vegye fel, ahol abbahagyta az események legfeljebb 7 napos.
* Az eseményközpont alapértelmezett fogyasztói csoportját kell használnia. Nincs szükség más fogyasztói csoportok létrehozására vagy egy külön fogyasztói csoport használatára, kivéve, ha azt tervezi, hogy két különböző eszköz ugyanazokat az adatokat használja fel ugyanabból az eseményközpontból.
* Az Azure-tevékenységnaplóhoz válasszon egy Event Hubs-névteret, és az Azure Monitor létrehoz egy eseményközpontot a névtérben, amelynek neve _insights-logs-operational-logs._ Más naplótípusok esetén választhat egy meglévő eseményközpontot, vagy beállíthatja, hogy az Azure Monitor naplókategóriánként hozzon létre egy eseményközpontot.
* Az 5671-es és 5672-es kimenő portot általában a számítógépen vagy az eseményközpontból adatokat fogyasztó virtuális hálózaton kell megnyitni.

## <a name="monitoring-data-available"></a>Rendelkezésre álló figyelési adatok
[Az Azure Monitor figyelési adatok forrásai](data-sources.md) ismertetik az Azure-alkalmazások különböző adatrétegeit és az egyes verziókhoz rendelkezésre álló figyelési adatok típusait. Az alábbi táblázat ismerteti az egyes szinteket, és ismerteti, hogyan továbbíthatók ezek az adatok egy eseményközpontba. Kövesse a további részleteket mutató hivatkozásokat.

| Szint | Adatok | Módszer |
|:---|:---|:---|
| [Azure-bérlő](data-sources.md#azure-tenant) | Az Azure Active Directory naplózási naplói | Konfigurálja a bérlői diagnosztikai beállítást az AAD-bérlőn. [Tekintse meg az oktatóanyagot: Az Azure Active Directory-naplók streamelése egy Azure-eseményközpontba](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) a részletekért. |
| [Azure-előfizetés](data-sources.md#azure-subscription) | Azure-tevékenységnapló | Hozzon létre egy naplóprofilt a Tevékenységnapló-események eseményközpontokba való exportálásához.  A részleteket az [Azure-platform naplóiból](resource-logs-stream-event-hubs.md) az Azure Event Hubs-ba tekintse meg. |
| [Azure-erőforrások](data-sources.md#azure-resources) | Platform-mutatók<br> Erőforrásnaplók |Mindkét típusú adat egy erőforrás-diagnosztikai beállítás használatával kerül elküldésre egy eseményközpontba. A részleteket [lásd: Stream Azure-erőforrás-naplók egy eseményközpontba.](resource-logs-stream-event-hubs.md) |
| [Operációs rendszer (vendég)](data-sources.md#operating-system-guest) | Azure virtuális gépek | Telepítse az [Azure Diagnosztikai bővítményt](diagnostics-extension-overview.md) Windows és Linux alapú virtuális gépekre az Azure-ban. Lásd: [Streamelési Azure diagnosztikai adatok a forró elérési úton az Event Hubs használatával](diagnostics-extension-stream-event-hubs.md) a Windows virtuális gépek és [a Linux diagnosztikai bővítmény segítségével a metrikák és naplók a](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) linuxos virtuális gépek részleteinek figyelése. |
| [Alkalmazás kódja](data-sources.md#application-code) | Application Insights | Az Application Insights nem biztosít közvetlen módszert az adatok eseményközpontokba való streamelésére. Beállíthatja az Application Insights-adatok [folyamatos exportálását](../../azure-monitor/app/export-telemetry.md) egy tárfiókba, majd egy logikai alkalmazás segítségével elküldheti az adatokat egy eseményközpontba a [Logic App manuális streamelése](#manual-streaming-with-logic-app)című szakaszban leírtak szerint. |

## <a name="manual-streaming-with-logic-app"></a>Manuális streamelés a Logic App alkalmazással
Olyan adatok esetében, amelyeket nem tud közvetlenül streamelni egy eseményközpontba, írhat az Azure storage-ba, majd használhat egy időaktiválású logikai alkalmazást, amely lekéri az adatokat a [blobstorage-ból,](../../connectors/connectors-create-api-azureblobstorage.md#add-action) és [leküldi azt üzenetként az eseményközpontba.](../../connectors/connectors-create-api-azure-event-hubs.md#add-action) 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partnereszközök az Azure Monitor integrációjával

A figyelési adatok egy eseményközpontba való átirányítása az Azure Monitor segítségével könnyedén integrálható külső SIEM-eszközökkel és figyelési eszközökkel. Példák az Azure Monitor-integrációval rendelkező eszközökre:

| Eszköz | Az Azure-ban üzemeltetve | Leírás |
|:---|:---| :---|
|  IBM QRadar | Nem | A Microsoft Azure DSM és a Microsoft Azure Event Hub Protocol letölthető [az IBM támogatási webhelyéről.](https://www.ibm.com/support) Az Azure-ral való integrációról a [QRadar DSM konfigurációban](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)olvashat bővebben. |
| Splunk | Nem | [Az Azure Monitor splunk bővítménye](https://splunkbase.splunk.com/app/3534/) egy nyílt forráskódú projekt, amely a Splunkbase-ben érhető el. A dokumentáció az [Azure Monitor Addon For Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)webhelyen érhető el.<br><br> Ha nem tud telepíteni egy bővítményt a Splunk-példányban, ha például proxyt használ, vagy a Splunk Cloud-on fut, továbbíthatja ezeket az eseményeket a Splunk HTTP Event Collector szolgáltatással az [Azure Function For Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS)használatával, amelyet az eseményközpontban lévő új üzenetek váltanak ki. |
| SumoLogic | Nem | A SumoLogic eseményközpontból származó adatok felhasználására vonatkozó beállítására vonatkozó utasítások [az Azure Audit App naplóinak gyűjtése az Event Hubról](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)című részében érhetők el. |
| ArcSight | Nem | Az ArcSight Azure Event Hub intelligens összekötő [az ArcSight intelligens összekötő gyűjtemény](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)részeként érhető el. |
| Syslog-kiszolgáló | Nem | Ha az Azure Monitor adatait közvetlenül egy syslog-kiszolgálóra szeretné streamelni, használhatja [az Azure-függvényen alapuló megoldást.](https://github.com/miguelangelopereira/azuremonitor2syslog/)
| LogRhythm között | Nem| A LogRhythm beállítására vonatkozó utasítások a naplók eseményközpontból történő gyűjtésére [itt](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)találhatók. 
|Logz.io | Igen | További információ: [A figyelés és a naplózás az Azure-ban futó Java-alkalmazások Logz.io használatával című témakörben talál.](https://docs.microsoft.com/azure/java/java-get-started-with-logzio)


## <a name="next-steps"></a>Következő lépések
* [A tevékenységnapló archiválása tárfiókba](../../azure-monitor/platform/archive-activity-log.md)
* [Az Azure-tevékenységnapló áttekintésének elolvasása](../../azure-monitor/platform/platform-logs-overview.md)
* [Riasztás beállítása tevékenységnapló-esemény alapján](../../azure-monitor/platform/alerts-log-webhook.md)


