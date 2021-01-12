---
title: Metrikák a Azure Monitor-ban – Azure Event Hubs | Microsoft Docs
description: Ez a cikk tájékoztatást nyújt arról, hogyan használható az Azure monitoring az Azure-Event Hubs figyeléséhez
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5b055c02783c40d844d1c6306bbb71cb23d602f2
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118795"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Event Hubs-metrikák az Azure Monitorban

Event Hubs mérőszámok Event Hubs erőforrások állapotát adja meg az Azure-előfizetésében. A metrikai adatok gazdag készletével a rendszer nem csak a névtér szintjén, hanem az entitás szintjén is felméri az esemény-hubok általános állapotát. Ezek a statisztikák fontosak lehetnek, mivel segítik az Event hubok állapotának figyelését. A metrikák az Azure-támogatáshoz való kapcsolódás nélkül is segíthetnek a hibák elhárításában.

A Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [Microsoft Azure figyelése](../azure-monitor/overview.md) és a [Azure monitor metrikáinak beolvasása](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a githubon .net-minta használatával.

## <a name="access-metrics"></a>Hozzáférési metrikák

Azure Monitor több módszert biztosít a metrikák eléréséhez. A metrikák a [Azure Portalon](https://portal.azure.com)keresztül érhetők el, vagy a Azure monitor API-k (REST és .net) és az Analysis Solutions (például Log Analytics és Event Hubs) használatával. További információ: [Azure monitor által összegyűjtött adatok figyelése](../azure-monitor/platform/data-platform.md).

A metrikák alapértelmezés szerint engedélyezve vannak, és a legutóbbi 30 nap adatait is elérheti. Ha hosszabb ideig kell megőriznie az adatokat, archiválhatja a metrikák adatait egy Azure Storage-fiókba. Ez a Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/platform/diagnostic-settings.md) van konfigurálva.


## <a name="access-metrics-in-the-portal"></a>Hozzáférési metrikák a portálon

A metrikák a [Azure Portalban](https://portal.azure.com)is megfigyelhetők. Az alábbi példa bemutatja, hogyan tekintheti meg a sikeres kérelmeket és a bejövő kérelmeket a fiók szintjén:

![Sikeres mérőszámok megtekintése][1]

A metrikákat közvetlenül a névtér használatával is elérheti. Ehhez válassza ki a névteret, majd kattintson a **metrikák** elemre. Az Event hub hatókörére szűrt metrikák megjelenítéséhez válassza ki az Event hub-t, majd kattintson a **metrikák** elemre.

A dimenziókat támogató metrikák esetében a kívánt dimenzió értékkel kell szűrnie a következő példában látható módon:

![Szűrés dimenzió értékkel][2]

## <a name="billing"></a>Számlázás

A Azure Monitor metrikáinak használata jelenleg ingyenes. Ha azonban olyan további megoldásokat használ, amelyek metrikai adatokat töltenek fel, akkor ezeket a megoldásokat számlázhatja. Ha például az Azure Storage-ba számít, ha egy Azure Storage-fiókba archiválja a metrikákat. Az Azure-t akkor is számlázza, ha a metrikák adatait a speciális elemzések Azure Monitor naplókra továbbítja.

A következő mérőszámok áttekintést nyújtanak a szolgáltatás állapotáról. 

> [!NOTE]
> Számos mérőszámot elavultunk, mert más néven vannak áthelyezve. Előfordulhat, hogy frissítenie kell a hivatkozásokat. Az "elavult" kulcsszóval jelölt mérőszámok nem lesznek támogatottak.

Minden metrikai érték Azure Monitor percenként lesz elküldve. Az idő részletessége határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes Event Hubs-metrika támogatott időintervalluma 1 perc.

## <a name="azure-event-hubs-metrics"></a>Azure Event Hubs mérőszámok
A szolgáltatás által támogatott mérőszámok listája: [Azure Event Hubs](../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)

## <a name="azure-monitor-integration-with-siem-tools"></a>Azure Monitor-integráció SIEM-eszközökkel
A figyelési adatok (tevékenységi naplók, diagnosztikai naplók stb.) útválasztása Azure Monitor lehetővé teszi, hogy könnyen integrálható legyen a biztonsági információkkal és az Event Management (SIEM) eszközeivel. További információt a következő cikkekben/blogbejegyzésekben talál:

- [Az Azure monitoring-adattovábbítása egy Event hub-ba külső eszköz általi felhasználás céljából](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Bevezetés a Azure Log Integrationba](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Integrálás a SIEM-eszközökkel az Azure Monitor használatával](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

Abban az esetben, ha egy SIEM-eszköz egy esemény központból használ naplózási adatokat, ha nem látja a beérkező üzeneteket, vagy ha a mérőszámok gráfban nem jelenik meg kimenő üzenet, kövesse az alábbi lépéseket:

- Ha nincsenek **beérkező üzenetek**, az azt jelenti, hogy a Azure monitor szolgáltatás nem helyezi át a naplózási/diagnosztikai naplókat az Event hub-ba. Ebben a forgatókönyvben nyisson meg egy támogatási jegyet a Azure Monitor csapatával. 
- Ha vannak bejövő üzenetek, de **nincsenek kimenő üzenetek**, az azt jelenti, hogy az Siem-alkalmazás nem olvassa be az üzeneteket. Forduljon az SIEM-szolgáltatóhoz, és ellenőrizze, hogy helyesek-e az Event hub konfigurációja.


## <a name="next-steps"></a>Következő lépések

* Lásd az [Azure monitoring áttekintése](../azure-monitor/overview.md)című témakört.
* [Azure monitor mérőszámok beolvasása .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) -minta használatával a githubon. 

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába oktatóanyag
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
