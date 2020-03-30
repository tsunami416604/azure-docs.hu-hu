---
title: Metrikák az Azure Monitorban – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk arról nyújt tájékoztatást, hogy miként használhatja az Azure Monitoring szolgáltatást az Azure Event Hubs figyelésére
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
ms.date: 09/18/2019
ms.author: shvija
ms.openlocfilehash: 96c346f4359740fda5638dfdbe5735c5bdfce8c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162650"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Event Hubs-metrikák az Azure Monitorban

Az Event Hubs-metrikák az Azure-előfizetésben lévő Event Hubs-erőforrások állapotát biztosítják. A metrikák adatainak gazdag készletével nem csak a névtér szintjén, hanem az entitás szintjén is felmérheti az eseményközpontok általános állapotát. Ezek a statisztikák fontosak lehetnek, mivel segítenek az eseményközpontok állapotának figyelésében. Metrikák is segít elhárítani a kiváltó problémákat anélkül, hogy kapcsolatba lépni az Azure-támogatás.

Az Azure Monitor egységes felhasználói felületeket biztosít a különböző Azure-szolgáltatások figyeléséhez. További információ: [Figyelés a Microsoft Azure-ban](../monitoring-and-diagnostics/monitoring-overview.md) és az [Azure Monitor metrikák lekérése a .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) minta a GitHubon.

## <a name="access-metrics"></a>Hozzáférési mutatók

Az Azure Monitor többféle módon érheti el a metrikákat. Metrikák at érhet el az [Azure Portalon](https://portal.azure.com)keresztül, vagy használhatja az Azure Monitor API-k (REST és .NET) és elemzési megoldások, például a Log Analytics és az Event Hubs. További információ: [Monitoring data collected by Azure Monitor](../azure-monitor/platform/data-platform.md).

A mérőszámok alapértelmezés szerint engedélyezve vannak, és a legutóbbi 30 napnyi adathoz férhet hozzá. Ha hosszabb ideig kell megőriznie az adatokat, archiválhatja a metrikák adatait egy Azure Storage-fiókba. Ez az Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/platform/diagnostic-settings.md) van konfigurálva.


## <a name="access-metrics-in-the-portal"></a>Hozzáférési mutatók a portálon

Az [Azure Portalon](https://portal.azure.com)idővel figyelheti a metrikákat. A következő példa bemutatja, hogyan tekintheti meg a sikeres kérelmeket és a bejövő kérelmeket a fiók szintjén:

![Sikeres mérőszámok megtekintése][1]

A metrikákat közvetlenül is elérheti a névtéren keresztül. Ehhez jelölje ki a névteret, majd kattintson **a Metrikák gombra.** Az eseményközpont hatókörére szűrt metrikák megjelenítéséhez jelölje ki az eseményközpontot, majd kattintson a **Metrikák gombra.**

A dimenziókat támogató mutatók esetében a kívánt dimenzióértékkel kell szűrnie a következő példában látható módon:

![Szűrő dimenzióértékkel][2]

## <a name="billing"></a>Számlázás

Metrikák használata az Azure Monitor ban jelenleg ingyenes. Azonban ha további megoldásokat használ, amelyek metrikaadatokat használnak, előfordulhat, hogy ezek a megoldások számlázásra kerül. Például az Azure Storage által számlázott, ha archiválja metrikák adatok egy Azure Storage-fiók. Akkor is az Azure-ban számláz, ha metrikák adatait továbbítja az Azure Monitor naplóiba a speciális elemzéshez.

Az alábbi mutatók áttekintést nyújtanak a szolgáltatás állapotáról. 

> [!NOTE]
> Több mutatót is elavulttal ítunk, mivel azok más néven kerülnek áthelyezésre. Ehhez szükség lehet a hivatkozások frissítésére. Az "elavult" kulcsszóval jelölt mérőszámok nem támogatottak a jövőben.

Az összes metrikaértékek percenként elküldésre kerülnek az Azure Monitornak. Az idő részletessége határozza meg azt az időintervallumot, amelyhez a metrikák értékei megjelennek. A támogatott időintervallum az összes Event Hubs metrikák 1 perc.

## <a name="request-metrics"></a>Mérőszámok kérése

Megszámolja az adat- és kezelési műveletek kérelmeinek számát.

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
| Bejövő kérések  | Az Azure Event Hubs szolgáltatásnak egy adott időszakban benyújtott kérések száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév |
| Sikeres kérelmek    | Az Azure Event Hubs szolgáltatásnak egy adott időszakban végrehajtott sikeres kérelmek száma. <br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév |
| Kiszolgálói hibák  | Az Azure Event Hubs szolgáltatás ban egy adott időszakban hiba miatt fel nem dolgozott kérelmek száma. <br/><br/>Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév |
|Felhasználói hibák |A megadott időszakban felhasználói hibák miatt fel nem dolgozott kérelmek száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Kvóta túllépési hibák |A kérelmek száma meghaladta a rendelkezésre álló kvótát. Az Event Hubs-kvótákról ebben a [cikkben](event-hubs-quotas.md) olvashat bővebben.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|

## <a name="throughput-metrics"></a>Átviteli mérőszámok

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
|Szabályozott kérelmek |Az átviteli egység felhasználási korlátjának túllépése miatt szabályozott kérések száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|

## <a name="message-metrics"></a>Üzenetmérő számok

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
|Bejövő üzenetek |Az Eseményközpontoknak egy adott időszakban küldött események vagy üzenetek száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Kimenő üzenetek |Az Eseményközpontokból egy adott időszakban beolvasott események vagy üzenetek száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Bejövő bájtok |Az Azure Event Hubs szolgáltatásnak egy adott időszakban küldött bájtok száma.<br/><br/> Egység: Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Kimenő bájtok |Az Azure Event Hubs szolgáltatásból egy adott időszakban lekért bájtok száma.<br/><br/> Egység: Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|

## <a name="connection-metrics"></a>Csatlakozási mutatók

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
|ActiveConnections |A névtérés az entitás aktív kapcsolatainak száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Megnyitott kapcsolatok |A nyitott kapcsolatok száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Kapcsolatok zárva |A lezárt kapcsolatok száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|

## <a name="event-hubs-capture-metrics"></a>Eseményközpontok rögzítési mutatói

Figyelheti az Eseményközpontok rögzítési mutatóit, ha engedélyezi az eseményközpontok rögzítési funkcióját. Az alábbi mérőszámok ismertetik, hogy mit figyelhet a rögzítés engedélyezve.

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
|Rögzítési hátralék |Azoknak a bájtoknak a száma, amelyekmég nem kerülnek rögzítésre a kiválasztott célhelyre.<br/><br/> Egység: Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Rögzített üzenetek |A megadott célhoz egy adott időszakban rögzített üzenetek vagy események száma.<br/><br/> Egység: Darabszám <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|
|Rögzített bájtok |A kiválasztott célhoz egy adott időszakban rögzített bájtok száma.<br/><br/> Egység: Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió: Entitásnév|

## <a name="metrics-dimensions"></a>Metrikadimenziók

Az Azure Event Hubs a következő dimenziókat támogatja az Azure Monitor metrikák. Dimenziók hozzáadása a metrikákhoz nem kötelező. Ha nem ad hozzá dimenziókat, a mérőszámok a névtér szintjén vannak megadva. 

| Metrikus neve | Leírás |
| ------------------- | ----------------- |
|Entitásnév| Az Event Hubs támogatja az eseményközpont-entitásokat a névtér alatt.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Az Azure Monitor integrációja SIEM-eszközökkel
A figyelési adatok (tevékenységnaplók, diagnosztikai naplók stb.) útválasztása egy eseményközpontba az Azure Monitor segítségével könnyedén integrálható a biztonsági információk és az eseménykezelési (SIEM) eszközökkel. További információt a következő cikkekben/blogbejegyzésekben talál:

- [Az Azure figyelési adatainak streamelése egy eseményközpontba külső eszköz általi felhasználás érdekében](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Bevezetés az Azure-naplóintegrációba](../security/fundamentals/azure-log-integration-overview.md)
- [Integrálás a SIEM-eszközökkel az Azure Monitor használatával](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

Abban az esetben, ha egy SIEM-eszköz naplóadatokat használ fel egy eseményközpontból, ha nem lát bejövő üzeneteket, vagy bejövő üzeneteket lát, de a metrikagrafikonon nem lát kimenő üzeneteket, kövesse az alábbi lépéseket:

- Ha **nincsenek bejövő üzenetek,** ez azt jelenti, hogy az Azure Monitor szolgáltatás nem helyezi át a naplózási/diagnosztikai naplókat az eseményközpontba. Ebben a forgatókönyvben nyisson meg egy támogatási jegyet az Azure Monitor csapatával. 
- ha vannak bejövő üzenetek, de **nincskimenő üzenet**, az azt jelenti, hogy a SIEM alkalmazás nem olvassa az üzeneteket. Lépjen kapcsolatba a SIEM-szolgáltatóval annak megállapítása érdekében, hogy az alkalmazások eseményközpontjának konfigurációja helyes-e.


## <a name="next-steps"></a>További lépések

* Tekintse meg az [Azure Monitoring áttekintését.](../monitoring-and-diagnostics/monitoring-overview.md)
* [Az Azure Monitor metrikák lekérése a GitHubon .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintával. 

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

- Bevezetés az Event Hubs használatába oktatóanyag
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



