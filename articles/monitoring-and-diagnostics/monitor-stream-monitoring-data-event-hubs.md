---
title: Stream Azure monitorozási adatok az Event hubs szolgáltatásba
description: Ismerje meg, hogyan streamelése az Azure monitorozási adatok az adatok importálása egy partneri SIEM-vagy elemzőeszköz egy eseményközpontba.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/06/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 5e8d8947643494e06faaabb5335c52df5908303e
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902989"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Stream Azure monitorozási adatok felhasználásra egy eseményközpontba egy külső eszközzel

Az Azure Monitor hozzáférni az összes monitorozási adatai az Azure-környezet, amely lehetővé teszi, hogy könnyedén létrehozhasson partneri SIEM és figyelési eszközök felhasználhatják az adatokat, egy folyamatot biztosít. Ez a cikk végigvezeti a beállítása az adatok különböző rétegek, az Azure-környezet küldendő egyetlen Event Hubs névtér vagy event hub, ahol begyűjthetők egy külső eszközzel.

## <a name="what-data-can-i-send-into-an-event-hub"></a>Milyen adatokat lehet küldeni egy eseményközpontba való? 

Az Azure-környezet van több "csomag" figyelési adatok, és a metódus az adathozzáférés az egyes szintekről-sablonoktól. Általában ezek a rétegek írható le:

- **Alkalmazásfigyelési adatok:** adatait a teljesítményének és funkcionalitásának írásos engedélye szükséges, és az Azure-ban futtatja a kódot. Alkalmazásfigyelési adatok közé a teljesítmény-nyomkövetés, alkalmazásnaplókat és telemetriai felhasználói. Alkalmazásfigyelési adatokat általában a következő módokon gyűjti:
  - Alakíthatja ki például a kód egy SDK-val a [Application Insights SDK](../application-insights/app-insights-overview.md).
  - A monitorozási ügynök, amely figyeli az új alkalmazás-naplókat a gépen futó az alkalmazás futtatásának, mint például a [Windows Azure diagnosztikai ügynök](./azure-diagnostics.md) vagy [Linux Azure diagnosztikai ügynök](../virtual-machines/linux/diagnostic-extension.md).
- **Vendég operációs rendszerek monitorozási adatai:** az operációs rendszer, amelyen fut az alkalmazás adatait. Példák a vendég operációs rendszer monitorozási adatok lenne a Linux rendszernaplójából vagy a Windows rendszer eseményeket. Ilyen típusú adatok gyűjtéséhez, például ügynököt telepíteni kell a [Windows Azure diagnosztikai ügynök](./azure-diagnostics.md) vagy [Linux Azure diagnosztikai ügynök](../virtual-machines/linux/diagnostic-extension.md).
- **Azure-erőforrás monitorozási adatai:** egy Azure-erőforrás a művelet adatait. Egyes Azure-erőforrástípus, például a virtuális gépek van egy vendég operációs rendszereket és figyelése, hogy az Azure szolgáltatáson belüli alkalmazások. Más Azure-erőforrások, például a hálózati biztonsági csoportok az erőforrás monitorozási adatok legmagasabb szintű rendelkezésre álló adatok (mivel az nem áll fenn a vendég operációs rendszer vagy alkalmazás ezeket az erőforrásokat futtató). Ezeket az adatokat lehessen gyűjteni használatával [erőforrás diagnosztikai beállításait](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Figyelési adatok az Azure platform:** a művelet és a egy Azure-előfizetés vagy a bérlő felügyeleti adatait, valamint állapotának és az Azure működésének adatait magát. A [tevékenységnapló](./monitoring-overview-activity-logs.md), beleértve a szolgáltatás egészségügyi adatok és az Active Directory naplózások példák a monitorozási adatok platform. Ezeket az adatokat, valamint a diagnosztikai beállítások használatával gyűjthetők össze.

Az egyik csomagunkban adatküldés egy eseményközpontba, ahol azt tölthetők be a partner eszközt. A következő szakaszokban az egyes szintekről az eseményközpontok felé is streamelhetők adatok konfigurálása. A lépések feltételezik, hogy már rendelkezik eszközöket lehet figyelni a rétegben.

## <a name="set-up-an-event-hubs-namespace"></a>Event Hubs-névtér beállítása

Mielőtt elkezdené, kell [hozzon létre egy Event Hubs-névtér és az eseményközpont](../event-hubs/event-hubs-create.md). A névtér és az eseményközpont az a hely összes monitorozási adatot. Event Hubs-névtér logikus csoportosításai, amelyek az ugyanazon hozzáférési szabályzatot az event hubs, sokkal például a tárolási fiók rendelkezik-e azon a fiókon belül az egyes blobok. Vegye figyelembe az event hubs-névtér és az Ön által létrehozott event hubs szolgáltatás néhány részleteit:
* Egy standard szintű Event Hubs-névtér használatát javasoljuk.
* Általában csak egy átviteli egységgel rendelkezhet szükség. Ha vertikális felskálázás a a napló használati növekedése van szüksége, mindig manuálisan később a névtér a kapacitásegységek számának növelése vagy automatikus infláció engedélyezése.
* Átviteli egységek száma az event hubs átviteli Méretezés növelése teszi lehetővé. A partíciók számának fogyasztás párhuzamosíthatja több ügyfél között teszi lehetővé. Megteheti, hogy egy partíció legfeljebb 20MBps vagy körülbelül másodpercenként 20 000 üzenetet. Attól függően, az eszközt, az adatok felhasználása valószínűleg vagy nem támogatja a több partícióról származó felhasználása. Ha nem biztos kapcsolatos beállítása a partíciók számát, azt javasoljuk négy partícióval.
* Azt javasoljuk, hogy üzenetmegőrzés beállíthatja az eseményközpontban, és 7 nap. Ha a felhasználó-eszköz számára több mint egy nap leáll, ez biztosítja, hogy az eszköz folytathatja a munkát, ahol abbahagyta (események legfeljebb 7 napos).
* Az eseményközpont az alapértelmezett felhasználói csoport használatát javasoljuk. Hiba esetén nem kell más felhasználói csoportok létrehozásához, vagy egy különálló fogyasztói csoportot használnak, kivéve, ha azt tervezi, hogy két különböző eszközökkel ugyanazokat az adatokat az azonos event hubs használata.
* Az Azure-tevékenységnapló kiválasztjuk az Event Hubs-névtér és az Azure Monitor létrehoz egy eseményközpontot az adott névtérben "insights-logs-operationallogs." nevű Minden olyan napló esetében, vagy választhat egy meglévő eseményközponton (így újból felhasználhatja az insights-logs-operationallogs ugyanazon eseményközpont), vagy rendelkezik log kategória szerinti event hub létrehozása az Azure Monitor.
* Általában 5671, 5672, és a portot kell megnyitni a gépen az eseményközpontból érkező adatok felhasználásához.

Emellett tekintse át a [Azure Event Hubs – gyakori kérdések](../event-hubs/event-hubs-faq.md).

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Hogyan állíthatok be monitorozási adatok az Azure platform egy eseményközpontba is streamelhetők?

Figyelési adatok az Azure platform két fő eseményforrást származik:
1. A [Azure tevékenységnapló](./monitoring-overview-activity-logs.md), amely tartalmazza a létrehozása, frissítése és törlési műveletek a Resource Manager, a változások [az Azure service health](../service-health/service-health-overview.md) , amely befolyásolhatja az erőforrást az előfizetésében, a [a resource health](../service-health/resource-health-overview.md) állapotváltásra, és számos egyéb típusú előfizetés-szintű eseményeit. [Ez a cikk részletesen jelennek meg az Azure-tevékenységnapló eseményeket az összes kategória](./monitoring-activity-log-schema.md).
2. [Jelentéskészítés az Azure Active Directory](../active-directory/active-directory-reporting-azure-portal.md), amely tartalmazza a bejelentkezési tevékenység és a naplózási beállításainak egy adott bérlőn belül végrehajtott módosítások előzményei. Ez még nem lehetséges az Azure Active Directory-adatok streamelése az event hubs-eseményközpontba.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Stream Azure tevékenységnapló adatainak eseményközpontba

Az Azure-tevékenységnapló adatok küldése az Event Hubs-névtér, beállíthatja a Naplóprofil az előfizetésén. [Ezt az útmutatót](./monitoring-stream-activity-logs-event-hubs.md) állíthat be egy Naplóprofil-előfizetésében. Ezt követően előfizetésenként figyelni szeretné.

> [!TIP]
> Egy Naplóprofil jelenleg csak lehetővé teszi, hogy válassza ki az Event Hubs-névtér, amelyben egy eseményközpont a neve "insights-operational-logs." jön létre Ez még nem adható meg a saját eseményközpontnév Log-profilban.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Hogyan állíthatok be Azure-erőforrás monitorozási adatai is streamelhetők az eseményközpontok felé?

Azure-erőforrás szolgáltat két típusú monitorozási adatait:
1. [Erőforrás-diagnosztikai naplók](./monitoring-overview-of-diagnostic-logs.md)
2. [Metrikák](monitoring-overview-metrics.md)

Mindkét típusú adatokat egy eseményközpontba egy erőforrás diagnosztikai beállításának érkeznek. [Ezt az útmutatót](./monitoring-stream-diagnostic-logs-to-event-hubs.md) állíthat be egy adott erőforrás az erőforrás diagnosztikai beállítást. Állítsa be, minden egyes erőforrás, amelyről el szeretné naplók gyűjtése az erőforrások diagnosztikai beállítása.

> [!TIP]
> Az Azure Policy segítségével győződjön meg arról, hogy egy adott hatókörön belül minden erőforrás mindig be van állítva a diagnosztikai beállítást [a DeployIfNotExists hatást a szabályzatbeli szabályban használatával](../azure-policy/policy-definition.md#policy-rule). Ma DeployIfNotExists csak támogatott beépített szabályzatokat.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>Hogyan állíthatok be vendég operációs rendszer figyelési adatokat egy eseményközpontba is streamelhetők?

Vendég operációs rendszer monitorozási adatok küldése eseményközpontba ügynököt telepíteni kell. Windows vagy Linux esetén adja meg az event hubs, valamint az event hubs, amelyhez az adatokat a konfigurációs fájlban kell küldeni, és adja át a konfigurációs fájlt az ügynököt a virtuális gépen futó küldendő kívánt adatokat.

### <a name="stream-linux-data-to-an-event-hub"></a>Stream Linux adatokat egy eseményközpontba

A [Linux Azure diagnosztikai ügynök](../virtual-machines/extensions/diagnostics-linux.md) küldéséhez használható monitorozási adatai egy Linux-gép az eseményközpontba. Ehhez adja hozzá az event hubs a LAD a fogadóként JSON védett fájl beállításait. [Ebben a cikkben további információt az event hub fogadó ad hozzá a Linux Azure diagnosztikai ügynök](../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Nem tudja beállítani streamelési vendég operációs rendszer monitorozási adatok a portálon egy eseményközpontba. Ehelyett manuálisan kell szerkeszteni a konfigurációs fájlban.

### <a name="stream-windows-data-to-an-event-hub"></a>Stream Windows adatokat egy eseményközpontba

A [Windows Azure diagnosztikai ügynök](./azure-diagnostics.md) küldéséhez használható monitorozási adatok egy Windows-gépről egy eseményközpontba. Ehhez adja hozzá az event hubs a WAD-konfigurációs fájl a privateConfig szakaszában fogadóként. [Ebben a cikkben további információt az event hub fogadó ad hozzá a Windows Azure diagnosztikai ügynök](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> Nem tudja beállítani streamelési vendég operációs rendszer monitorozási adatok a portálon egy eseményközpontba. Ehelyett manuálisan kell szerkeszteni a konfigurációs fájlban.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>Hogyan alkalmazás figyelési adatok is streamelhetők eseményközpontba beállítása?

Alkalmazásfigyelési adatokat igényel, hogy a kód kialakítva az SDK-t, ezért nincs útválasztási alkalmazásfigyelési adatokat egy eseményközpontba, az Azure-beli általános célú megoldást. Azonban [Azure Application Insights](../application-insights/app-insights-overview.md) egy olyan szolgáltatás, amely használható az Azure alkalmazásszintű adatok gyűjtésére. Ha az Application Insights használatával figyelési adatokat egy eseményközpontba streamelheti az alábbiak szerint:

1. [Állítsa be a folyamatos exportálás](../application-insights/app-insights-export-telemetry.md) , az Application Insights-adatok a storage-tárfiókba.

2. Állítsa be egy időzítő által aktivált logikai alkalmazást, amely [kér le adatokat a blob storage-ból](../connectors/connectors-create-api-azureblobstorage.md#add-action) és [leküldi az event hubs egy üzenetnek számít](../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Mire használhatom a figyelési adatok my eseményközpontnak küldött?

A figyelési adatok útválasztást egy eseményközpontba, és az Azure Monitor lehetővé teszi könnyen integrálhatja a partneri SIEM és figyelési eszközöket. A legtöbb eszközök az eseményközpont kapcsolati karakterláncával és bizonyos engedélyeket adatokat olvasni az event hubs az Azure-előfizetés szükséges. A következő eszközök az Azure Monitor integrációja nem teljes listája:

* **Az IBM QRadar** – a Microsoft Azure DSM-et és a Microsoft Azure Event Hub protokoll is letölthető [az IBM-támogatási webhely](http://www.ibm.com/support). Is [további információ az integráció itt az Azure-ral](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** -Splunk konfigurációtól függően kétféleképpen:
    1. [Az Azure Monitor bővítmény Splunk](https://splunkbase.splunk.com/app/3534/) Splunkbase és a egy nyílt forráskódú projekt érhető el. [Dokumentáció az itt](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Ha kiegészítő szolgáltatást nem lehet telepíteni a Splunk-példány (például) Ha proxyt használ, vagy a Splunk-felhőben futó), ezeket az eseményeket a Splunk HTTP Eseménygyűjtő történő továbbítás [ezt az eseményközpontban lévő üzenetek által aktivált függvény](https://github.com/Microsoft/AzureFunctionforSplunkVS).
* **SumoLogic** – egy adott eseményközpontból adatok felhasználásához SumoLogic beállításával kapcsolatos utasítások [itt érhető el](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>További lépések
* [A tárfiókhoz a tevékenységnapló archiválása](monitoring-archive-activity-log.md)
* [Olvassa el az Azure-tevékenységnapló áttekintése](monitoring-overview-activity-logs.md)
* [Egy tevékenységnapló eseményéhez alapuló riasztás beállítása](insights-auditlog-to-webhook-email.md)

