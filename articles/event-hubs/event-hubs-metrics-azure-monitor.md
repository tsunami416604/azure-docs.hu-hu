---
title: Metrikák a Azure Monitor-ban – Azure Event Hubs | Microsoft Docs
description: Ez a cikk tájékoztatást nyújt arról, hogyan használható az Azure monitoring az Azure-Event Hubs figyeléséhez
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 76df9920c517e8370409bca26e5a6533c7253c1e
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85314590"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Event Hubs-metrikák az Azure Monitorban

Event Hubs mérőszámok Event Hubs erőforrások állapotát adja meg az Azure-előfizetésében. A metrikai adatok gazdag készletével a rendszer nem csak a névtér szintjén, hanem az entitás szintjén is felméri az esemény-hubok általános állapotát. Ezek a statisztikák fontosak lehetnek, mivel segítik az Event hubok állapotának figyelését. A metrikák az Azure-támogatáshoz való kapcsolódás nélkül is segíthetnek a hibák elhárításában.

A Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [Microsoft Azure figyelése](../monitoring-and-diagnostics/monitoring-overview.md) és a [Azure monitor metrikáinak beolvasása](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a githubon .net-minta használatával.

## <a name="access-metrics"></a>Hozzáférési metrikák

Azure Monitor több módszert biztosít a metrikák eléréséhez. A metrikák a [Azure Portalon](https://portal.azure.com)keresztül érhetők el, vagy a Azure monitor API-k (REST és .net) és az Analysis Solutions (például Log Analytics és Event Hubs) használatával. További információ: [Azure monitor által összegyűjtött adatok figyelése](../azure-monitor/platform/data-platform.md).

A metrikák alapértelmezés szerint engedélyezve vannak, és a legutóbbi 30 nap adatait is elérheti. Ha hosszabb ideig kell megőriznie az adatokat, archiválhatja a metrikák adatait egy Azure Storage-fiókba. Ez a Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/platform/diagnostic-settings.md) van konfigurálva.


## <a name="access-metrics-in-the-portal"></a>Hozzáférési metrikák a portálon

A metrikák a [Azure Portalban](https://portal.azure.com)is megfigyelhetők. Az alábbi példa bemutatja, hogyan tekintheti meg a sikeres kérelmeket és a bejövő kérelmeket a fiók szintjén:

![Sikeres mérőszámok megtekintése][1]

A metrikákat közvetlenül a névtér használatával is elérheti. Ehhez válassza ki a névteret, majd kattintson a **metrikák**elemre. Az Event hub hatókörére szűrt metrikák megjelenítéséhez válassza ki az Event hub-t, majd kattintson a **metrikák**elemre.

A dimenziókat támogató metrikák esetében a kívánt dimenzió értékkel kell szűrnie a következő példában látható módon:

![Szűrés dimenzió értékkel][2]

## <a name="billing"></a>Számlázás

A Azure Monitor metrikáinak használata jelenleg ingyenes. Ha azonban olyan további megoldásokat használ, amelyek metrikai adatokat töltenek fel, akkor ezeket a megoldásokat számlázhatja. Ha például az Azure Storage-ba számít, ha egy Azure Storage-fiókba archiválja a metrikákat. Az Azure-t akkor is számlázza, ha a metrikák adatait a speciális elemzések Azure Monitor naplókra továbbítja.

A következő mérőszámok áttekintést nyújtanak a szolgáltatás állapotáról. 

> [!NOTE]
> Számos mérőszámot elavultunk, mert más néven vannak áthelyezve. Előfordulhat, hogy frissítenie kell a hivatkozásokat. Az "elavult" kulcsszóval jelölt mérőszámok nem lesznek támogatottak.

Minden metrikai érték Azure Monitor percenként lesz elküldve. Az idő részletessége határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes Event Hubs-metrika támogatott időintervalluma 1 perc.

## <a name="request-metrics"></a>Kérelmek metrikái

Megszámolja az adatok és a felügyeleti műveleti kérelmek számát.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Bejövő kérelmek  | Az Azure Event Hubs szolgáltatásnak küldött kérések száma egy adott időszakban. <br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName |
| Sikeres kérések    | Az Azure Event Hubs szolgáltatásnak küldött sikeres kérések száma egy adott időszakban. <br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName |
| Kiszolgálói hibák  | Az Azure Event Hubs szolgáltatás hibája miatt nem feldolgozott kérelmek száma egy adott időszakban. <br/><br/>Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName |
|Felhasználói hibák |A megadott időszakban felhasználói hibák miatt nem feldolgozott kérelmek száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|A kvóta túllépte a hibákat |A kérések száma túllépte a rendelkezésre álló kvótát. A Event Hubs kvótákkal kapcsolatos további információkért tekintse meg [ezt a cikket](event-hubs-quotas.md) .<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|

## <a name="throughput-metrics"></a>Átviteli sebesség mérőszámai

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Szabályozott kérelmek |Az átviteli egység felhasználási korlátjának túllépése miatt szabályozott kérések száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|

## <a name="message-metrics"></a>Üzenet metrikái

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Bejövő üzenetek |A megadott időszakban Event Hubs küldött események vagy üzenetek száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Kimenő üzenetek |A Event Hubsból beolvasott események vagy üzenetek száma egy adott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Bejövő bájtok |Az Azure Event Hubs szolgáltatásnak megadott időszakon keresztül eljuttatott bájtok száma.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Kimenő bájtok |Az Azure Event Hubs szolgáltatásból beolvasott bájtok száma egy adott időszakban.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|

## <a name="connection-metrics"></a>Kapcsolatok metrikái

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Aktív kapcsolatai |A névtérben található aktív kapcsolatok száma, valamint az entitások.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Megnyitott kapcsolatok |A nyitott kapcsolatok száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Lezárt kapcsolatok |A lezárt kapcsolatok száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs rögzítési metrikák

Az Event hubok rögzítési funkciójának engedélyezésével figyelheti Event Hubs rögzítési metrikáit. A következő mérőszámok leírják, hogy a rögzítés engedélyezve legyen.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Várakozó fájlok rögzítése |A kiválasztott célhoz még nem rögzített bájtok száma.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Rögzített üzenetek |A kiválasztott célhelyre rögzített üzenetek vagy események száma egy adott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Rögzített bájtok száma |A kiválasztott célhelyre a megadott időszakban rögzített bájtok száma.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák méretei

Az Azure Event Hubs a Azure Monitor metrikáinak következő dimenzióit támogatja. Nem kötelező dimenziókat hozzáadni a metrikához. Ha nem ad hozzá dimenziókat, a metrikák a névtér szintjén vannak megadva. 

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|EntityName| Event Hubs támogatja az Event hub-entitásokat a névtér alatt.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Azure Monitor-integráció SIEM-eszközökkel
A figyelési adatok (tevékenységi naplók, diagnosztikai naplók stb.) útválasztása Azure Monitor lehetővé teszi, hogy könnyen integrálható legyen a biztonsági információkkal és az Event Management (SIEM) eszközeivel. További információt a következő cikkekben/blogbejegyzésekben talál:

- [Az Azure monitoring-adattovábbítása egy Event hub-ba külső eszköz általi felhasználás céljából](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Bevezetés a Azure Log Integrationba](../security/fundamentals/azure-log-integration-overview.md)
- [Integrálás a SIEM-eszközökkel az Azure Monitor használatával](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

Abban az esetben, ha egy SIEM-eszköz egy esemény központból használ naplózási adatokat, ha nem látja a beérkező üzeneteket, vagy ha a mérőszámok gráfban nem jelenik meg kimenő üzenet, kövesse az alábbi lépéseket:

- Ha nincsenek **beérkező üzenetek**, az azt jelenti, hogy a Azure monitor szolgáltatás nem helyezi át a naplózási/diagnosztikai naplókat az Event hub-ba. Ebben a forgatókönyvben nyisson meg egy támogatási jegyet a Azure Monitor csapatával. 
- Ha vannak bejövő üzenetek, de **nincsenek kimenő üzenetek**, az azt jelenti, hogy az Siem-alkalmazás nem olvassa be az üzeneteket. Forduljon az SIEM-szolgáltatóhoz, és ellenőrizze, hogy helyesek-e az Event hub konfigurációja.


## <a name="next-steps"></a>További lépések

* Lásd az [Azure monitoring áttekintése](../monitoring-and-diagnostics/monitoring-overview.md)című témakört.
* [Azure monitor mérőszámok beolvasása .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -minta használatával a githubon. 

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába oktatóanyag
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



