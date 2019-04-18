---
title: Az Azure Monitor - metrikák az Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk információt nyújt az Azure Monitoring segítségével figyelheti az Azure Event Hubs
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: b5df69e9670c01b576afe242b39532acb1e1c526
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58849375"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Az Azure Event Hubs-metrikák az Azure monitorban

Event Hubs-mérőszámok az Azure-előfizetésében az Event Hubs-erőforrások állapotának biztosítja. Metrikai adatok széles skáláját felmérheti az event hubs szolgáltatás nem csak a névterek szintjén, hanem az entitások szintjén általános állapotát. A statisztikai lehet fontos, mert ezek segítségével figyelheti az event hubs szolgáltatás állapotát. Metrikák is segít kiváltó problémák elhárítása anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Az Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [a Microsoft Azure figyelés](../monitoring-and-diagnostics/monitoring-overview.md) és a [lekérése az Azure Monitor-metrikák .NET-tel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. Mindkét hozzáférési metrikák keresztül is a [az Azure portal](https://portal.azure.com), vagy használja az Azure Monitor API-k (REST és .NET) és elemzési megoldásokkal, például az Operation Management Suite és az Event Hubs. További információkért lásd: [figyelési adatokat gyűjtött az Azure Monitor](../azure-monitor/platform/data-platform.md).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és elérheti az utolsó 30 nap adatait. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ennek a konfigurációja a [diagnosztikai beállítások](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) az Azure monitorban.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés mérőszámok portálon

Az idő függvényében segítségével figyelheti a mérőszámokat a [az Azure portal](https://portal.azure.com). Az alábbi példa bemutatja, hogyan sikeres kérések és a fiók szintjén a bejövő kérelmek megtekintése:

![A sikeres metrikáinak megtekintése][1]

Metrikák a névtér keresztül közvetlenül is elérheti. Ehhez válassza ki a névteret, és kattintson a **metrikák**. Az event hubs hatókörének szűrt metrikák megjelenítéséhez válassza ki az event hubs, és kattintson a **metrikák**.

Dimenziók támogató metrikákhoz a kívánt dimenzió értékű a következő példában látható módon kell szűrése:

![A dimenzió értéket szűrése][2]

## <a name="billing"></a>Számlázás

Metrikák az Azure Monitor használata jelenleg ingyenes. Azonban használ további megoldásokat, amelyek fogadni a mérőszámadatokat, akkor előfordulhat, hogy után kell díjat ezek a megoldások által. Ha például számítjuk fel az Azure Storage által archiválja mérőszámok az Azure Storage-fiók. Emellett számítjuk fel az Azure-ban, adatfolyam-metrikák adatait az Azure Monitor naplóira fejlett analitikai Ha.

Az alábbi mérőszámok segítségével a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt rendszer kivezetése több metrikát. Ehhez szükség lehet, hogy a hivatkozások frissítését. Metrikák, a "elavult" kulcsszó megjelölve a jövőben nem támogatja.

Az összes értékeihez kapnak az Azure Monitor percenként. Az idő részletessége határozza meg az időintervallum, amelynek metrikák jelennek meg. Minden Event Hubs-metrika támogatott idő időköz 1 perc.

## <a name="request-metrics"></a>Kérelem-metrikák

Számít az adatok és a felügyeleti műveletek kérések száma.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| A bejövő kérések (előnézet) | Az Azure Event Hubs szolgáltatás egy megadott időszakban végrehajtott kérelmek száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName |
| Sikeres kérések (előnézet)   | Az Azure Event Hubs szolgáltatás egy megadott időszakban végrehajtott sikeres kérelmek száma. <br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName |
| Kiszolgálóhibák (előzetes verzió) | Az Azure Event Hubs szolgáltatás hibája miatt a meghatározott időtartamon belül nem feldolgozott kérelmek száma. <br/><br/>Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName |
|Felhasználói hibák (előzetes verzió)|Egy meghatározott időtartamon belül a felhasználói hibák miatt nem feldolgozott kérelmek száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Kvóta túllépve hibák (előzetes verzió)|A kérelmek száma túllépte a rendelkezésre álló kvótát. Lásd: [Ez a cikk](event-hubs-quotas.md) Event Hubs-kvótákról további információt.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|

## <a name="throughput-metrics"></a>Átviteli sebességre vonatkozó mérőszámok

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Szabályozott kérelmeinek száma (előzetes verzió)|Voltak szabályozva, mert túllépte az átviteli egységek használati kérelmek száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|

## <a name="message-metrics"></a>Üzenet-metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Bejövő üzenetek (előzetes verzió)|Események vagy adott időszakon belül az Event hubs szolgáltatásba küldött üzenetek számát.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Kimenő üzenetek (előzetes verzió)|Események vagy üzenetek száma egy meghatározott időtartamon belül beolvasni az Event hubs Eseményközpontokból.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Bejövő bájtok száma (előzetes verzió)|Az Azure Event Hubs szolgáltatás egy meghatározott időtartamon belül küldött bájtok száma.<br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Kimenő bájtok (előzetes verzió)|Az Azure Event Hubs szolgáltatás egy meghatározott időtartamon belül veszi bájtok száma.<br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|

## <a name="connection-metrics"></a>Kapcsolati metrika

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Aktív kapcsolatai (előzetes verzió)|A névtér, valamint egy entitására aktív kapcsolatok száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Kapcsolatok megnyitott (előzetes verzió)|Megnyitott kapcsolatok száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Kapcsolat lezárva (előzetes verzió)|Lezárt kapcsolatok száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs Capture metrikák

Az Event Hubs Capture metrikák figyelheti az event hubs rögzítési funkciója engedélyezésekor. A következő metrikák ismertetik, mit is figyelheti a rögzítés engedélyezve van.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Rögzítése (előzetes verzió)|Amely során a kiválasztott cél még el bájtok száma.<br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Rögzített üzenetek (előzetes verzió)|Az üzenetek vagy a kiválasztott cél egy megadott időszakban rögzített események száma.<br/><br/> Szervezeti egység: Darabszám <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|
|Rögzített bájtok száma (előzetes verzió)|A kiválasztott cél egy megadott időszakban rögzített bájtok száma.<br/><br/> Szervezeti egység: Bájt <br/> Aggregation Type: Összes <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Azure Event hubs szolgáltatás a következő dimenziókat támogatja a mérőszámok az Azure Monitor. A metrikák dimenziók hozzáadása nem kötelező. Ha nem adja hozzá a dimenziók, a metrikák a névterek szintjén vannak megadva. 

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|EntityName| Az Event Hubs névtér event hub entitása támogatja.|

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).
* [.NET-tel az Azure Monitor-metrikák beolvasása](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon. 

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



