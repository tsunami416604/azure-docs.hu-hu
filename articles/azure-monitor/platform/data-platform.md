---
title: Az Azure Monitor adatplatform |} A Microsoft Docs
description: Figyelési adatok az Azure Monitor által gyűjtött elkülönített metrikákat, amelyek a könnyen használható, és képes a közel valós idejű felhasználói helyzetek és fejlett elemzési által használt naplókat.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790926"
---
# <a name="azure-monitor-data-platform"></a>Az Azure Monitor-adatplatform

Minden réteget és az elosztott rendszer minden összetevője a működési adatok gyűjtésének observability teszi lehetővé a mai összetett számítástechnikai környezetek, amelyek mind a felhőbeli, mind a helyszíni szolgáltatások, az elosztott alkalmazások futtatása szükséges. Sebességkorlátokat hajthatja végre ezeket az adatokat és a egy egyetlen ablaktáblába, és a számos érdekelt felek támogatása a szervezet programban különböző szempontok szerint tekinthesse oldalairól egységesen kell.

[Az Azure Monitor](../overview.md) gyűjt, és a azokat egy közös adatplatformot, ahol használat elemzés, a Vizualizáció és a riasztások a különböző forrásokból összesíti az adatokat. Felett több forrásból származó adatokat is lehetőséget kínál, részletes elemzések a figyelt erőforrások és adatok mellett is, amely tárolja az adatokat az Azure Monitor más szolgáltatások egységes élményt biztosít.


![Az Azure Monitor áttekintése](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Az Azure monitorban observability adatok
Metrikák, naplók és nyomkövetések elosztott gyakran nevezik, observability három pillérre. Ezek a monitorozási eszköz kell gyűjtése és elemzése biztosít a figyelt rendszer elegendő observability adatok különböző fajtáit. Több területei legyenek elérhetők az adatok korrelálásához és a teljes készletet figyelés alatt álló adatok összesítésének observability érhető el. Mivel az Azure Monitor együtt több forrásból származó adatokat tárolja, az adatok összekapcsolhatók legyenek, és elemzi a közös eszközök használatával. Több Azure-előfizetések és a bérlők mellett egyéb szolgáltatások adatait tartalmazó adatok is utal.

Azure-erőforrások jelentős részét a monitorozási adatok létrehozásához. Az Azure Monitor összesíti az adatokat, és figyelést vagy egyéb forrásokból származó adatok egy metrikákat vagy naplókat platform. Minden egyes figyelési forgatókönyvek az adott van optimalizálva, és az Azure monitorban támogatják a különböző funkciókat. Például az adatelemzés, a Vizualizációk vagy a riasztási funkciók szükséges különbségek megértéséhez, hogy az adott helyzetben a legtöbb hatékonyabbak és költségkímélőbbek módon valósítható meg. Elemzése az Azure Monitor, mint például [Application Insights](../app/app-insights-overview.md) vagy [-beli virtuális gépek az Azure Monitor](../insights/vminsights-overview.md) rendelkezik elemzési eszközökkel hajthat végre, amelyek lehetővé teszik az adott figyelési forgatókönyv összpontosíthat, nem kell megérteni a a két típusú adatok közötti különbségeket. 


### <a name="metrics"></a>Mérőszámok
[Metrikák](data-platform-metrics.md) időben bizonyos elemeit a rendszer egy adott időpontban leíró numerikus érték. Összegyűjtött rendszeres időközönként és időbélyeget, egy nevet, egy értéket és egy vagy több meghatározó címkék jelöli. Metrikák különféle algoritmusok, más metrikákkal képest, és idővel a trendek elemzése összesíthetők. 

Metrikák az Azure monitorban vannak tárolva egy idősorozat-adatbázis, amely időbélyeggel ellátott adatelemzés számára van optimalizálva. Ez lehetővé teszi metrikák, különösen olyan riasztások, és gyors problémák felismerése. Akkor mondja el, hogy a rendszer hajt végre, de általában kell naplókat a problémák kiváltó okának azonosításához értékkel egyesítve jön létre.

Metrikák érhetők el az Azure Portalon, az interaktív elemzés céljából [Metrikaböngésző](../app/metrics-explorer.md). Hozzáadhat egy [Azure irányítópultján](../learn/tutorial-app-dashboards.md) vizualizációs és más adatok együttes alkalmazásával és közel valós idejű használt [riasztási](alerts-metric.md).

További információt az Azure Monitor metrikák többek között azok az adatok forrásai [metrikák az Azure monitorban](data-platform-metrics.md).

### <a name="logs"></a>Naplók
[Naplók](data-platform-logs.md) olyan események, amelyek a rendszeren belül történt. Ezeket tartalmazhat különböző típusú adatokkal, és előfordulhat, hogy strukturálni vagy szabad formátumú szöveges időbélyegzővel ellátott. Esemény a környezetben hoz létre a naplóbejegyzéseket, és a rendszer nagy terhelés alatt általában generál további naplózási kötet azok csak időnként lehet létrehozni.

Az Azure Monitor-naplók vannak tárolva a Log Analytics-munkaterület alapján [Azure adatkezelő](/azure/data-explorer/) biztosítja a hatékony elemzési motor és [részletes lekérdezési nyelvet](/azure/kusto/query/). Naplók általában teljes kontextusba azonosítják a probléma elegendő információt tartalmaznak, és értékes legfelső szintű kis-és a problémák azonosításához.

> [!NOTE]
> Fontos, az Azure Monitor naplóira és források az Azure-ban naplóadatok megkülönböztetésére. Előfizetés hibaszintű eseményeket az Azure-ban írt, például egy [tevékenységnapló](activity-logs-overview.md) , amely az Azure Monitor menüből is megtekintheti. A legtöbb erőforrást fog kiírni, hogy működési információkat egy [diagnosztikai napló](diagnostic-logs-overview.md) , amelyek különböző helyen is továbbíthatja. Az Azure Monitor naplóira egy log adatplatform, amely összegyűjti a tevékenységnaplókat, és diagnosztikai naplókat és más figyelési adatokhoz biztosítsa a teljes erőforráskészletét lecserélhetik részletes elemzést.


 Használhatja a [lekérdezések naplózását](../log-query/log-query-overview.md) interaktív módon a [Log Analytics](../log-query/portals.md) az Azure Portalon, vagy adja hozzá az eredményeket egy [Azure irányítópultján](../learn/tutorial-app-dashboards.md) vizualizációs együtt más adatok. Ezenkívül létrehozhat [naplóriasztások](alerts-log.md) amely a lekérdezés eredményeit, egy ütemezés alapján riasztást aktivál.

További információt az Azure Monitor naplóira, beleértve azok az adatok forrásai [naplók az Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Elosztott nyomkövetési
Nyomok, amelyek egy felhasználói kérelem egy elosztott rendszeren keresztül kapcsolódó események sorát. Alkalmazáskód viselkedését és más tranzakciók teljesítményének meghatározására használható. Naplók milyen gyakran fogja létrehozni egy elosztott rendszer egyes összetevőinek, miközben a nyomkövetés méri a a művelet és az alkalmazás teljesítményét különböző összetevők teljes készlete.

Az Azure monitorban elosztott nyomkövetést engedélyezve van a [Application Insights SDK](../app/distributed-tracing.md), és nyomkövetési adatokat tárolja és Application Insights által gyűjtött egyéb alkalmazás naplóadatokat. Így a más naplóadatokat, beleértve a naplólekérdezések, irányítópultok és riasztások azonos elemzési eszközök számára elérhető.

Tudjon meg többet a nyomkövetés elosztott [elosztott nyomkövetést mi?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Hasonlítsa össze az Azure Monitor-metrikák és naplók

Az alábbi táblázat összehasonlítja a metrikák és naplók az Azure monitorban.

| Attribútum  | Mérőszámok | Naplók |
|:---|:---|:---|
| Előnyök | Könnyen használható, és közel valós idejű forgatókönyveket, például a riasztás képes. Ideális megoldás a problémák gyors felismerése. | Elemezheti a gazdag lekérdezési nyelv. Ideális megoldás a részletes elemzés és gyökerének azonosítása. |
| Adatok | Csak a numerikus értékek | Szöveg- vagy numerikus adatok |
| struktúra | Mintavételi időköz, a figyelt erőforrások, a numerikus érték például tulajdonságok szabványos készletét. Bizonyos metrikák további definíciós több dimenziókat tartalmaznak. | A napló típusától függően tulajdonságai egyedi készletét. |
| Gyűjtemény | Gyűjtött rendszeres időközönként. | Előfordulhat, hogy gyűjtendő szórványosan események értékhez létrejön egy rekord. |
| Az Azure Portalon megtekintése | Metrikaböngésző | Log Analytics |
| Adatforrások | Azure-erőforrások gyűjtött metrikák platform.<br>Az Application Insights által figyelt alkalmazások.<br>Alkalmazás- vagy API által definiált egyéni. | Alkalmazás- és diagnosztikai naplókat.<br>Figyelési megoldások.<br>Ügynökök és a Virtuálisgép-bővítmények.<br>Alkalmazás kérelmeket és kivételeket.<br>Az Azure Security Center.<br>Adatgyűjtő API. |

## <a name="collect-monitoring-data"></a>Figyelési adatok gyűjtése
Különböző [adatforrásokat az Azure Monitor](data-sources.md) fognak írni, a Log Analytics-munkaterület (naplók) vagy az Azure Monitor metrikák adatbázis (metrikák) vagy mindkettőt. Egyes források beállításfájlra közvetlenül ezek adattárak, míg mások előfordulhat, hogy egy másik helyre, például az Azure storage írása, és feltölti a naplók és mérőszámok némi konfigurálást igényelnek. 

Lásd: [metrikák az Azure monitorban](data-platform-metrics.md) és [naplók az Azure Monitor](data-platform-logs.md) töltse fel az egyes típusok különböző adatforrások listáját.


## <a name="stream-data-to-external-systems"></a>Stream data a külső rendszerekkel
Mellett az Azure-ban az eszközök használatával elemezheti a monitorozási adatok, szükség lehet a követelmény, hogy egy külső eszköz, például a biztonsági információk és az esemény (SIEM) termékben továbbítja. Közvetlenül a figyelt erőforrások keresztül általában történik a továbbító [Azure Event Hubs](/azure/event-hubs/). Egyes források konfigurálható adatküldéshez közvetlenül az eseményközpontok felé, miközben használhatja például a logikai alkalmazás egy másik folyamat a szükséges adatok lekéréséhez. Lásd: [Stream Azure monitorozási adatok felhasználásra egy eseményközpontba egy külső eszközzel](stream-monitoring-data-event-hubs.md) részleteiről.



## <a name="next-steps"></a>További lépések

- Tudjon meg többet [metrikák az Azure monitorban](data-platform-metrics.md).
- Tudjon meg többet [naplók az Azure Monitor](data-platform-logs.md).
- További információ a [monitorozási adatok elérhető](data-sources.md) a különböző erőforrásokat az Azure-ban.
