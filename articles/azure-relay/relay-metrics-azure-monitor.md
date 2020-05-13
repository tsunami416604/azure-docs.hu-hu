---
title: Azure Relay metrikák a Azure Monitorban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Monitor az Azure Relay állapotának figyelésére.
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211800"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Monitor Azure Relay metrikák 
Azure Relay mérőszámok az Azure-előfizetésében lévő erőforrások állapotát adják meg. A metrikai adatok gazdag készletével felmérhető a továbbítási erőforrások általános állapota, nem csak a névtér szintjén, hanem az entitás szintjén is. Ezek a statisztikák fontosak lehetnek, mivel segítenek a Azure Relay állapotának figyelésében. A metrikák az Azure-támogatáshoz való kapcsolódás nélkül is segíthetnek a hibák elhárításában.

A Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [Microsoft Azure figyelése](../monitoring-and-diagnostics/monitoring-overview.md) és a [Azure monitor metrikáinak beolvasása](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a githubon .net-minta használatával.

> [!IMPORTANT]
> Ez a cikk csak a Azure Relay Hibrid kapcsolatok szolgáltatására vonatkozik, nem a WCF Relay. 

## <a name="access-metrics"></a>Hozzáférési metrikák

Azure Monitor több módszert biztosít a metrikák eléréséhez. A mérőszámokat a [Azure Portalon](https://portal.azure.com)keresztül érheti el, vagy használhatja a Azure monitor API-kat (REST és .net) és az elemzési megoldásokat, például az Operation Management Suite-t és a Event Hubst. További információ: [Azure monitor által összegyűjtött adatok figyelése](../azure-monitor/platform/data-platform.md).

A metrikák alapértelmezés szerint engedélyezve vannak, és a legutóbbi 30 nap adatait is elérheti. Ha hosszabb ideig kell megőriznie az adatokat, archiválhatja a metrikák adatait egy Azure Storage-fiókba. Ez a Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/platform/diagnostic-settings.md) van konfigurálva.

## <a name="access-metrics-in-the-portal"></a>Hozzáférési metrikák a portálon

A metrikák a [Azure Portalban](https://portal.azure.com)is megfigyelhetők. Az alábbi példa bemutatja, hogyan tekintheti meg a sikeres kérelmeket és a bejövő kérelmeket a fiók szintjén:

![][1]

A metrikákat közvetlenül a névtér használatával is elérheti. Ehhez válassza ki a névteret, majd kattintson a * * mérőszámok * * elemre. 

A dimenziókat támogató metrikák esetében a kívánt dimenzió értékkel kell szűrnie.

## <a name="billing"></a>Számlázás

A Azure Monitor metrikáinak használata jelenleg előzetes verzióban ingyenes. Ha azonban olyan további megoldásokat használ, amelyek metrikai adatokat töltenek fel, akkor ezeket a megoldásokat számlázhatja. Ha például az Azure Storage-ba számít, ha egy Azure Storage-fiókba archiválja a metrikákat. A Azure Monitor naplók számlázása akkor is történik, ha a metrikák adatait a speciális elemzéshez Azure Monitor naplókba.

A következő mérőszámok áttekintést nyújtanak a szolgáltatás állapotáról. 

> [!NOTE]
> Számos mérőszámot elavultunk, mert más néven vannak áthelyezve. Előfordulhat, hogy frissítenie kell a hivatkozásokat. Az "elavult" kulcsszóval jelölt mérőszámok nem lesznek támogatottak.

Minden metrikai érték Azure Monitor percenként lesz elküldve. Az idő részletessége határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes Azure Relay-metrika támogatott időintervalluma 1 perc.

## <a name="connection-metrics"></a>Kapcsolatok metrikái

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Figyelőkapcsolatokra – sikeres  | Egy adott időszakban Azure Relay sikeres figyelő-kapcsolatok száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Figyelőkapcsolatokra – vonatkozó ügyfélhibái |A figyelő kapcsolatainak száma a megadott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Figyelőkapcsolatokra – ServerError |A figyelő kapcsolatainak száma a megadott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Feladói kapcsolatokra – sikeres |A megadott időszakban sikeres küldő kapcsolatok száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Feladói kapcsolatokra – vonatkozó ügyfélhibái |Az ügyfél hibáinak száma a küldő kapcsolatain a megadott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Feladói kapcsolatokra – ServerError |A megadott időszakon belül a küldő kapcsolaton futó kiszolgálói hibák száma.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Figyelőkapcsolatokra – TotalRequests |A figyelő kapcsolatainak teljes száma egy adott időszakban.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Feladói kapcsolatokra – TotalRequests |A küldők által megadott időszakon keresztül kezdeményezett kapcsolati kérelmek.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Aktív kapcsolatai |Az aktív kapcsolatok száma. Ez az érték egy időponthoz tartozó érték.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|ActiveListeners |Az aktív figyelők száma. Ez az érték egy időponthoz tartozó érték.<br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|Bontásai |A leválasztott figyelők száma egy adott időszakban.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|
|SenderDisconnects |A leválasztott küldők száma egy adott időszakban.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|

## <a name="memory-usage-metrics"></a>Memóriahasználat mérőszámai

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|BytesTransferred |A megadott időszakon át továbbított bájtok száma.<br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák méretei

A Azure Relay a Azure Monitor metrikáinak következő dimenzióit támogatja. Nem kötelező dimenziókat hozzáadni a metrikához. Ha nem ad hozzá dimenziókat, a metrikák a névtér szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|EntityName| Azure Relay támogatja az üzenetküldési entitásokat a névtérben.|

## <a name="next-steps"></a>További lépések

Lásd az [Azure monitoring áttekintése](../monitoring-and-diagnostics/monitoring-overview.md)című témakört.

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




