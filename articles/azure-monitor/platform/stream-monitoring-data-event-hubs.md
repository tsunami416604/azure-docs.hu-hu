---
title: Azure monitoring-adatstreamek továbbítása az Event hub szolgáltatásba
description: Ismerje meg, hogyan továbbíthatja az Azure monitoring-adatait egy Event hub-ba az ADATPARTNER SIEM vagy Analytics eszközbe való beszerzéséhez.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: f282fce8070d440bdd3a518b4444eb0e67110961
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559065"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Azure monitoring-adatstreamek továbbítása az Event hub szolgáltatásba
A Azure Monitor teljes körű figyelési megoldást kínál az Azure-ban, más felhőben és a helyszínen található alkalmazások és szolgáltatások számára. Az adatok elemzéséhez és a különböző figyelési helyzetekben való kihasználásához Azure Monitor használata mellett előfordulhat, hogy a környezetében más figyelési eszközökre is el kell küldenie. A legtöbb esetben az [Azure Event Hubs](/azure/event-hubs/)-t használja a leghatékonyabb módszer a megfigyelési és a külső eszközökre való továbbításra. Ez a cikk rövid leírást tartalmaz arról, hogy miként továbbíthatja a különböző forrásokból származó figyelési adatok egy Event hubhoz való továbbítását, és részletes útmutatásra mutató hivatkozásokat talál.


## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az adatforrások folyamatos átvitelének konfigurálása előtt [létre kell hoznia egy Event Hubs névteret és az Event hub](../../event-hubs/event-hubs-create.md)-t. Ez a névtér és az Event hub az összes megfigyelési adattal kapcsolatos cél. Az Event Hubs névtér olyan esemény-hubok logikai csoportosítása, amelyek ugyanazt a hozzáférési házirendet használják, hasonlóan ahhoz, hogy a Storage-fiókhoz egyedi Blobok tartoznak. Vegye figyelembe a következő adatokat az Event hubok névterével és a figyelési adatok továbbításához használt esemény hubokkal kapcsolatban:

* Az átviteli egységek száma lehetővé teszi az adatforgalom léptékének növelését az Event hubok esetében. Általában csak egy átviteli egységre van szükség. Ha vertikális felskálázásra van szükség a naplók használatának növekedésével, manuálisan növelheti a névtér átviteli egységének számát, vagy engedélyezheti az automatikus inflációt.
* A partíciók száma lehetővé teszi, hogy integrálással a felhasználást számos fogyasztó között. Egy partíció legfeljebb 20MBps vagy körülbelül 20 000 üzenetet tud támogatni másodpercenként. Az adatok felhasználását igénybe vehető eszköztől függően előfordulhat, hogy nem támogatja több partíció használatát. Négy partíciót érdemes elindítani, ha nem biztos abban, hogy nem biztos benne, hogy hány partíciót kell beállítania.
* Az üzenetek megőrzését az Event hub-ban legalább 7 napig be kell állítani. Ha a felhasználó eszköz több mint egy napja leáll, ezzel biztosíthatja, hogy az eszköz képes legyen a 7 napnál régebbi események kikapcsolására.
* Az Event hub alapértelmezett fogyasztói csoportját kell használnia. Nem kell más fogyasztói csoportokat létrehoznia, vagy külön fogyasztói csoportot használnia, ha azt tervezi, hogy két különböző eszköz ugyanazt az adatközpontot használja fel ugyanazokból az adatokból.
* Az Azure-beli tevékenység naplójában válasszon ki egy Event Hubs névteret, és Azure Monitor a névtéren belül létrehoz egy, az elemzések – _naplók – műveleti naplók_nevű Event hubot. Más típusú naplók esetében választhat egy meglévő Event hub-t, vagy létrehozhat egy Azure Monitor Event hub-t is.
* A 5671-es és 5672-as kimenő portot általában a számítógépen kell megnyitni, vagy az VNET az adatok felhasználását kell megnyitnia.


## <a name="monitoring-data-available"></a>Figyelési adathozzáférés
A [Azure monitor figyelési adatforrásai](data-sources.md) az Azure-alkalmazásokhoz tartozó különböző adatszinteket, valamint az egyes szolgáltatásokhoz rendelkezésre álló megfigyelési adattípusokat ismertetik. Az alábbi táblázat felsorolja ezeket a szinteket, valamint annak leírását, hogy az egyes események hogyan továbbíthatók az Event hub-ba. További részletekért kövesse a megadott hivatkozásokat.

| Szint | Adatok | Módszer |
|:---|:---|:---|
| [Azure-bérlő](data-sources.md#azure-tenant) | Naplók Azure Active Directory | Adja meg a bérlői diagnosztikai beállítást a HRE-bérlőn. További részletekért lásd az [oktatóanyag: Stream Azure Active Directory naplók az Azure Event hub-](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) ba című témakört. |
| [Azure-előfizetés](data-sources.md#azure-subscription) | Azure-tevékenységnapló | Hozzon létre egy log-profilt a műveletnapló eseményeinek Event Hubsba való exportálásához.  További részletekért lásd: az [Azure Activity napló exportálása a Storage-ba vagy az azure Event Hubs](activity-log-export.md) . |
| [Azure-erőforrások](data-sources.md#azure-resources) | Platform metrikái<br>Diagnosztikai naplók |A rendszer mindkét típusú adattípust egy erőforrás-diagnosztikai beállítás használatával küldi el az Event hub-nak. További részletekért tekintse meg az [Azure diagnosztikai naplóinak továbbítása az Event hub-](resource-logs-stream-event-hubs.md) ban című témakört. |
| [Operációs rendszer (vendég)](data-sources.md#operating-system-guest) | Azure-alapú virtuális gépek | Telepítse a [Azure Diagnostics bővítményt](diagnostics-extension-overview.md) az Azure-beli Windows-és Linux-alapú virtuális gépeken. A Windows rendszerű virtuális gépekkel kapcsolatos részletekért tekintse meg [a gyakori Event Hubs elérésű útvonalon található Streaming Azure Diagnostics adatokat](diagnostics-extension-stream-event-hubs.md) , és a Linux rendszerű virtuális gépekkel kapcsolatos részletekért [használja a linuxos diagnosztikai bővítményt](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) . |
| [Alkalmazás kódja](data-sources.md#application-code) | Application Insights | A Application Insights nem biztosít közvetlen metódust az adattovábbításhoz az Event hubokba. Beállíthatja a Application Insights-információk [folyamatos exportálását](../../azure-monitor/app/export-telemetry.md) egy Storage-fiókba, majd egy logikai alkalmazás használatával elküldheti az adatátvitelt az Event hubhoz a [logikai alkalmazás manuális továbbítása](#manual-streaming-with-logic-app)című témakörben leírtak szerint. |

## <a name="manual-streaming-with-logic-app"></a>Manuális átvitel a logikai alkalmazással
Ha olyan adatokra van szüksége, amelyek közvetlenül nem továbbíthatók az Event hubhoz, írhat az Azure Storage-ba, majd egy olyan idővezérelt logikai alkalmazást használhat, amely lekéri a [blob Storage-ból származó adatait](../../connectors/connectors-create-api-azureblobstorage.md#add-action) , és [üzenetet küld az Event hub](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)-nak. 


## <a name="tools-with-azure-monitor-integration"></a>Eszközök Azure Monitor integrációval

A monitorozási adatait Azure Monitor segítségével átirányíthatja egy Event hubhoz, így könnyen integrálható a külső SIEM-és monitorozási eszközökkel. Azure Monitor integrációs eszközök például a következők:

| Eszköz | Leírás |
|:---|:---|
|  IBM QRadar | A Microsoft Azure DSM és Microsoft Azure Event hub protokoll letölthető [az IBM támogatási webhelyéről](https://www.ibm.com/support). Az Azure-nal való integrációról a [QRADAR DSM-konfigurációjában](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)olvashat bővebben. |
| Splunk | [A Splunk Azure monitor-bővítménye](https://splunkbase.splunk.com/app/3534/) egy nyílt forráskódú projekt, amely elérhető a Splunkbase-ben. A dokumentáció a következő címen érhető el: [Azure monitor Addon for splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Ha nem telepíthet bővítményt a splunk-példányban, például ha proxyt használ, vagy ha a splunk-felhőben fut, ezeket az eseményeket a [splunk Azure Function](https://github.com/Microsoft/AzureFunctionforSplunkVS)használatával továbbíthatja a splunk http-esemény gyűjtője számára, amelyet az Event hub új üzenetei is aktiválnak. |
| SumoLogic | Az SumoLogic adatok az Event hub-ból való felhasználásának beállítására vonatkozó utasítások [Az Azure-beli audit-alkalmazás eseménynaplójában érhetők el az Event hub-ból](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | A ArcSight Azure Event hub intelligens összekötő a [ArcSight intelligens összekötő gyűjteményének](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)részeként érhető el. |
| Syslog-kiszolgáló | Ha Azure Monitor-adatforrást közvetlenül egy syslog-kiszolgálóra szeretné továbbítani, használhat egy [Azure-függvényen alapuló megoldást](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | [Itt](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)találhat útmutatást a naplók LogRhythm való összegyűjtéséhez. 


## <a name="next-steps"></a>Következő lépések
* [A tevékenység naplójának archiválása egy Storage-fiókba](../../azure-monitor/platform/archive-activity-log.md)
* [Olvassa el az Azure-tevékenység naplójának áttekintését](../../azure-monitor/platform/activity-logs-overview.md)
* [Riasztás beállítása egy tevékenység naplójának eseménye alapján](../../azure-monitor/platform/alerts-log-webhook.md)


