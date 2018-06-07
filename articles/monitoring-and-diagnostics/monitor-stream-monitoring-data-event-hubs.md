---
title: Adatfolyam-figyelési adatok Event hubs Azure |} Microsoft Docs
description: 'Útmutató: az adatfolyamként küldje el az eseményközpontba megkapják a partner SIEM adatimportáláshoz vagy alkalmazáselemző eszköz, amely a Azure figyelési adatok.'
author: johnkemnetz
manager: robb
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/05/2018
ms.author: johnkem
ms.openlocfilehash: 35cdd157469556c071b03a0f25184df141057554
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639061"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>A figyelés egy eseményközpontba felhasználásra adatok külső eszköz adatfolyam Azure

Az Azure biztosít, egyetlen feldolgozási sorként összes figyelési adatok az Azure-alapú környezetben, így egyszerűen állítsa be a partner SIEM és figyelési eszközök adatok felhasználásához hozzáférést. Ez a cikk végigvezeti a állít be, az adatok különböző rétegek az Azure-alapú környezetben kell küldeni egy egy Event Hubs névtér vagy esemény központi, ahol begyűjthetők egy külső eszközzel.

## <a name="what-data-can-i-send-into-an-event-hub"></a>Milyen adatokat lehet küldeni eseményközpontnak? 

Az Azure környezetben több "réteg" figyelési adatok, és az adatok elérése az egyes rétegek metódusában kis mértékű. Általában ezek a rétegek jelentheti:

- **Figyelési adatok alkalmazás:** teljesítményét és funkcióit írt, és az Azure-on futtatja a kódot az adatait. Alkalmazás figyelési adatok például a teljesítmény nyomkövetési alkalmazásnaplók és felhasználói telemetriai adatokat. Alkalmazás figyelési adatok általában gyűjtött a program a következő módszerek valamelyikével:
  - Az SDK-val a kód például tagolása által a [Application Insights SDK](../application-insights/app-insights-overview.md).
  - Egy figyelési ügynök, amely figyeli az új alkalmazás naplózza a számítógépen futtassa az alkalmazást futtató, például a [Windows Azure diagnosztikai ügynök](./azure-diagnostics.md) vagy [Linux Azure diagnosztikai ügynök](../virtual-machines/linux/diagnostic-extension.md).
- **Figyelési adatok vendég operációs rendszer:** az operációs rendszer, amelyen fut az alkalmazás adatait. Vendég operációs rendszer figyelési adatok lenne a Linux rendszernaplójába bejegyzett és a Windows rendszer eseményeket. Ilyen típusú adatok gyűjtésére, például az ügynök telepítéséhez szüksége a [Windows Azure diagnosztikai ügynök](./azure-diagnostics.md) vagy [Linux Azure diagnosztikai ügynök](../virtual-machines/linux/diagnostic-extension.md).
- **Figyelési adatok Azure-erőforrás:** egy Azure-erőforrás működésével kapcsolatos adatokat. Bizonyos Azure erőforrástípusokra, például a virtuális gépek létrejön egy vendég operációs rendszer és a alkalmazás(ok) belül, hogy az Azure szolgáltatás figyelése. Egyéb Azure-erőforrások, például a hálózati biztonsági csoportok az erőforrás figyelési adatok esetén érhetők el adatok a legmagasabb szint (mivel nincs, a vendég operációs rendszer vagy a források futó alkalmazások). Ezek az adatok gyűjthetők használatával [erőforrás diagnosztikai beállításainak](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Figyelési adatok Azure platformon:** a művelet és az Azure-előfizetés vagy bérlői felügyeletével kapcsolatos adatokat, valamint az ügyfélállapot és a művelet az Azure data magát. A [tevékenységnapló](./monitoring-overview-activity-logs.md), beleértve a szolgáltatás állapotának adatai, és az Active Directory naplózás példák a figyelési adatok platform. Ezeket az adatokat, valamint a diagnosztikai beállítások használatával gyűjthetők össze.

Bármely rétegtől adatküldés eseményközpontnak, ahol azt is kell húzni a partner eszközt. A következő szakaszok ismertetik, hogyan konfigurálhatja az egyes rétegek az eseményközpontba közzétett adatokat. A lépések azt feltételezik, hogy már rendelkezik a eszközök a figyelni kívánt adott réteg sablonbeállításait.

## <a name="set-up-an-event-hubs-namespace"></a>Az Event Hubs névtér beállítása

Kezdés előtt kell [létrehoz egy Event Hubs-névteret és esemény-központot](../event-hubs/event-hubs-create.md). A névtér és az event hub az a hely összes figyelési adatait. Az Event Hubs névtere a logikai csoportosításhoz, amelyek ugyanabban a hozzáférési házirendben az event hubs, sokkal tárolási például VMM-szolgáltatásfióknak egyes blobok a tárfiókon belül. Vegye figyelembe az event hubs névtér és az event hubs az Ön által létrehozott néhány részleteit:
* A szabványos Event Hubs-névtér használatát javasoljuk.
* Csak egy átviteli egységgel rendelkezhet általában szükséges. Ha vertikális felskálázás a a napló használati növekedése van szüksége, mindig manuálisan később a névtér átviteli egységek számának növelése vagy automatikus infláció engedélyezése.
* Átviteli egységek száma lehetővé teszi az event hubs átviteli méret növelése érdekében. A partíciók számának fogyasztás parallelize sok fogyasztók keresztül teszi lehetővé. Egy olyan partíciót teheti legfeljebb 20MBps vagy körülbelül 20 000 üzenetek száma másodpercenként. Attól függően, hogy az eszközt, az adatok felhasználásához lehetséges, hogy vagy nem támogatja a több partícióról származó fel. Ha nem biztos kapcsolatos beállítása a partíciók száma, javasoljuk négy partíciót.
* Azt javasoljuk, hogy be kell állítani üzenet megőrzési az eseményközpont 7 nap. Ha a felhasználó-eszköz nem működik több mint egy nappal, ez biztosítja, hogy az eszköz is onnan folytathatja az adatgyűjtést, ahol abbahagyta (események legfeljebb 7 napos).
* Az alapértelmezett felhasználói csoport az eseményközpont használatát javasoljuk. Nincs szükség más fogyasztói csoportok létrehozására, vagy használjon egy külön felhasználói csoport, kivéve, ha azt tervezi, szeretné, hogy az azonos adatokat az azonos eseményközpontból két különböző eszközök.
* Az Azure tevékenységnapló az Event Hubs névtér választja, és Azure figyelő létrehoz egy eseményközpontot, az adott névtérben, úgynevezett "insights-logs-operationallogs." A többi napló típusú választhat egy meglévő eseményközpont (hogy lehetővé teszi az ugyanazon insights-logs-operationallogs event hubs újból), vagy létrehoz egy eseményközpontot napló kategóriánként Azure figyelő.
* Általában 5671 és 5672 portot kell megnyitni a számítógépen az eseményközpontból adatok felhasználásához.

Is lásd: a [Azure Event Hubs gyakran ismételt kérdések](../event-hubs/event-hubs-faq.md).

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Hogyan állíthatom be Azure platformon figyelési adatok adatfolyamként eseményközpontokba való továbbítását?

Figyelési adatok Azure platformon két fő eseményforrást származik:
1. A [Azure tevékenységnapló](./monitoring-overview-activity-logs.md), létrehozása, amely tartalmazza, frissítési és törlési műveletek a Resource Manager, a változások [Azure szolgáltatás állapota](../service-health/service-health-overview.md) , amely befolyásolhatja az előfizetéséhez, a erőforrások[erőforrás állapota](../service-health/resource-health-overview.md) Állapotváltások, és számos egyéb típusú előfizetés szintű eseményeket. [Ez a cikk részletezi az eseményeket, amelyek az Azure tevékenységnapló jelennek meg minden kategóriáinak](./monitoring-activity-log-schema.md).
2. [Az Azure Active Directory jelentéskészítési](../active-directory/active-directory-reporting-azure-portal.md), a bejelentkezési tevékenység és a hitelesítési eljárást az egy adott bérlő belül végrehajtott módosítások előzményeit, amely tartalmazza. Nincs még eseményközpontnak adatfolyam Azure Active Directory-adatokat is.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Az adatfolyam Azure tevékenység naplóadatok eseményközpontba

Az Azure tevékenységnapló az Event Hubs névtérbe adatok elküldéséhez a állít be egy napló profil az előfizetéshez. [Kövesse a jelen útmutató](./monitoring-stream-activity-logs-event-hubs.md) előfizetéséhez napló profil beállítása. Ehhez előfizetésenként a figyelni kívánt.

> [!TIP]
> A napló profil jelenleg csak kiválasztását teszi egy Event Hubs névtér, amelyben az eseményközpontok hozza létre a neve "insights-műveleti-naplókat." Nincs még lehetővé a saját eseményközpont neveként adja meg a napló-profilban.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Hogyan állíthatom be Azure-erőforrás figyelési adatok adatfolyamként eseményközpontokba való továbbítását?

Azure-erőforrások figyelési adatok két típusa hozható létre:
1. [Erőforrás diagnosztikai naplók](./monitoring-overview-of-diagnostic-logs.md)
2. [Metrikák](monitoring-overview-metrics.md)

Mindkét típusú adatok küldése az erőforrás diagnosztikai beállításainak eseményközpontba. [Kövesse a jelen útmutató](./monitoring-stream-diagnostic-logs-to-event-hubs.md) állíthat be egy erőforrás diagnosztikai beállításának egy adott erőforráshoz. Az egyes erőforrások, amelyből el kívánja naplógyűjtéshez erőforrás diagnosztikai beállítás megadása.

> [!TIP]
> Azure-házirendek segítségével győződjön meg arról, hogy minden egyes hatókörön belüli erőforrás mindig be van állítva a diagnosztikai beállítású [DeployIfNotExists hatása a házirendi szabály használatával](../azure-policy/policy-definition.md#policy-rule). Ma DeployIfNotExists csak a beépített házirendek támogatják.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>Hogyan állíthatom be vendég operációs rendszer figyelési adatok adatfolyamként eseményközpontokba való továbbítását?

Vendég operációs rendszer figyelési adatok küldése eseményközpontba ügynököt telepíteni szeretné. A Windows vagy Linux adja meg az eseményközpont, valamint az event hubs, amelyre az adatokat küldjön a konfigurációs fájlban, és konfigurációs fájl át az ügynököt a virtuális gépen küldendő kívánt adatokat.

### <a name="stream-linux-data-to-an-event-hub"></a>Az eseményközpontok felé Linux adatok folyamatos átviteléhez

A [Linux Azure diagnosztikai ügynök](../virtual-machines/extensions/diagnostics-linux.md) küldéséhez használható figyelési adatok Linux számítógépről egy eseményközpontba. Ehhez az event hubs hozzáadása a fogadó a LAD a JSON védett fájl beállításait. [Ebben a cikkben találhat további információt a eseményelnyelő hub hozzáadása a Linux Azure diagnosztikai ügynök](../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Nem állítható be adatfolyamként való küldése a vendég operációs rendszer figyelési adatokat az eseményközpontok felé, a portálon. Ehelyett manuálisan kell szerkeszteni a konfigurációs fájlban.

### <a name="stream-windows-data-to-an-event-hub"></a>Az eseményközpontok felé Windows adatok folyamatos átviteléhez

A [Windows Azure diagnosztikai ügynök](./azure-diagnostics.md) küldéséhez használható figyelési adatokat a Windows-gépről az eseményközpontba. Ehhez az event hubs egy fogadó a privateConfig szakaszban a ÜVEGVATTA konfigurációs fájl hozzáadása. [Ebben a cikkben találhat további információt a eseményelnyelő hub hozzáadása a Windows Azure diagnosztikai ügynök](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> Nem állítható be adatfolyamként való küldése a vendég operációs rendszer figyelési adatokat az eseményközpontok felé, a portálon. Ehelyett manuálisan kell szerkeszteni a konfigurációs fájlban.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>Hogyan állíthatom be eseményközpontba közzétett alkalmazás figyelési adatokat?

Figyelési adatok az alkalmazáshoz, hogy a kód tagolva az SDK-val, így nem létezik a figyelési adatok az Azure-ban egy eseményközpontba útválasztási alkalmazás általános célú megoldást. Azonban [Azure Application Insights](../application-insights/app-insights-overview.md) egy olyan szolgáltatás, amely használható Azure alkalmazásszintű adatok gyűjtéséért felelős ügyfélfeladatot. Az Application Insights használatakor a következő módon is adatfolyam formájában a figyelési adatok az eseményközpontok felé:

1. [A folyamatos exportálás beállítása](../application-insights/app-insights-export-telemetry.md) az Application Insights adatok tárfiókba.

2. Állítson be egy időzítő indított logikai alkalmazás, amely [lekéri az adatokat a blob storage](../connectors/connectors-create-api-azureblobstorage.md#add-action) és [kimenő üzenetben az event hubs](../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Mit tehetek küldi el az event hubs a figyelési adatokat?

A figyelési adatokat az Azure-megfigyelővel eseményközpontba útválasztási lehetővé teszi, könnyen integrálható a partner SIEM és figyelési eszközök. A legtöbb eszközök az event hub kapcsolati karakterlánc és bizonyos adatokat olvasni az event hubs az Azure-előfizetéshez engedélyekkel van szükség. Az Azure-figyelő integráció eszközök nem teljes listája itt található:

* **Az IBM QRadar** -letölthető a Microsoft Azure DSM-et és a Microsoft Azure Event Hub protokoll [az IBM támogatási webhely](http://www.ibm.com/support). Is [tudhat meg többet az az Azure-integráció Itt](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** -Splunk beállításoktól függően kétféleképpen:
    1. [Az Azure figyelő bővítménye Splunk](https://splunkbase.splunk.com/app/3534/) Splunkbase és nyílt forráskódú projektként érhető el. [Dokumentáció a következő helyen](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Ha egy bővítmény nem telepíti a Splunk példányát (például) Ha proxyt használ, vagy Splunk felhőben futó), ezeket az eseményeket a Splunk HTTP Eseménygyűjtő történő továbbítás [ezt a funkciót, amelyet új üzeneteket az eseményközpontba](https://github.com/sebastus/AzureFunctionForSplunkVS).
* **SumoLogic** -az eseményközpontban lévő adatok felhasználásához SumoLogic beállításával kapcsolatos útmutatás [érhető el itt](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>További lépések
* [A műveletnapló tárfiókba archiválása](monitoring-archive-activity-log.md)
* [Olvassa el az Azure tevékenységnapló áttekintése](monitoring-overview-activity-logs.md)
* [Tevékenységnapló esemény alapján riasztás beállítása](insights-auditlog-to-webhook-email.md)

