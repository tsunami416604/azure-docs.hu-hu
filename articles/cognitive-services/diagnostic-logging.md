---
title: Diagnosztikai naplózás
titleSuffix: Azure Cognitive Services
description: Ez az útmutató részletes útmutatást nyújt az Azure Cognitive Services számára a diagnosztikai naplózás engedélyezése. Ezek a naplók gazdag, gyakori vonatkozó adatokkal szolgálnak a műveletet olyan erőforrás probléma azonosítása és a hibakereséshez használt.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 3be912f053bf206999546678e1e407548af181bf
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657676"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Az Azure Cognitive Services diagnosztikai naplózás engedélyezése

Ez az útmutató részletes útmutatást nyújt az Azure Cognitive Services számára a diagnosztikai naplózás engedélyezése. Ezek a naplók gazdag, gyakori vonatkozó adatokkal szolgálnak a műveletet olyan erőforrás probléma azonosítása és a hibakereséshez használt. A folytatás előtt rendelkeznie kell egy Azure-előfizetés legalább egy Cognitive Services-szolgáltatás, a fiók például [a Bing webes keresés](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [beszédszolgáltatások](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), vagy [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Előfeltételek

Diagnosztikai naplózás engedélyezéséhez kell valahol a naplóadatokat tárolni. Ez az oktatóanyag az Azure Storage és a Log Analytics használja.

* [Az Azure storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) -megőrzi a diagnosztikai naplók szabályzati naplózás, a statikus elemzés vagy a biztonsági mentés. A storage-fiók nem rendelkezik és a naplókat kibocsátó mindaddig, amíg a beállítást konfiguráló felhasználónak megfelelő hozzáférése RBAC mindkét előfizetéshez erőforrásnak ugyanabban az előfizetésben lennie.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) – olyan rugalmas log keresést és elemzést eszköz, amely lehetővé teszi, hogy egy Azure-erőforrás által létrehozott nyers naplók elemzése.

> [!NOTE]
> További konfigurációs lehetőségek állnak rendelkezésre. További tudnivalókért lásd: [gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Diagnosztikai naplók összegyűjtése engedélyezése  

Először engedélyezése a diagnosztikai naplózás az Azure portal használatával.

> [!NOTE]
> Ez a funkció a PowerShell vagy az Azure CLI használatával engedélyezze az szereplő utasítások [gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#diagnostic-settings).

1. Keresse meg az Azure Portalon. Majd keresse meg és válassza ki a Cognitive Services-erőforrás. Ha például a webes keresési Bing-előfizetés.   
2. Ezután a bal oldali navigációs menüben található **figyelés** válassza **diagnosztikai beállítások**. Ezen a képernyőn az erőforráshoz tartozó összes korábban létrehozott diagnosztikai beállításait tartalmazza.
3. Ha egy korábban létrehozott erőforrás, amely szeretné használni, kiválaszthatja azt most. Ellenkező esetben válassza **+ diagnosztikai beállítás hozzáadása**.
4. Adjon meg egy nevet a beállítás. Válassza ki **archiválás tárfiókba** és **küldeni a log Analytics**.
5. Amikor a rendszer kéri, konfigurálásához, válassza ki a storage-fiók és a diagnosztikai naplók tárolásához használni kívánt OMS-munkaterülethez. **Megjegyzés**: Ha nem rendelkezik egy storage-fiók vagy az OMS-munkaterülethez, kövesse az utasításokat követve hozzon létre egyet.
6. Válassza ki **naplózási**, **operace RequestResponse**, és **AllMetrics**. Ezután állítsa be a diagnosztikai naplóadatokat megőrzési időtartama. Adatmegőrzési értéke nulla, ha a napló kategória események határozatlan ideig tárolódnak.
7. Kattintson a **Save** (Mentés) gombra.

Mielőtt a naplózási adatok lekérdezéséhez és kielemzéséhez érhető el akár két óráig is eltarthat. Ezért ne aggódjon, ha nem lát semmilyen azonnal.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Megtekintheti és diagnosztikai adatok exportálása az Azure Storage-ból

Az Azure Storage egy robusztus objektumtárolási megoldása, amely nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. Ebben a szakaszban megismerheti az összes tranzakciós storage-fiók lekérdezése egy 30 napos időtartamon keresztül, és az Excel-adatok exportálása.

1. Az Azure Portalon keresse meg az Azure Storage-erőforrás, amely az előző szakaszban létrehozott.
2. A bal oldali navigációs menüben található **figyelés** válassza **metrikák**.
3. A rendelkezésre álló legördülő menük segítségével konfigurálhatja a lekérdezést. Ebben a példában állítsa az időtartományt **az elmúlt 30 nap** és a metrika az **tranzakció**.
4. Ha a lekérdezés befejeződött, megjelenik egy vizualizációt az elmúlt 30 napban tranzakció. Ezek az adatok exportálásához használja a **exportálása Excelbe** gombra az oldal tetején található.

További tudnivalók a Mire képes a diagnosztikai adatok [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Naplók megtekintése a Log Analyticsben

Ismerkedés a log analytics-adatok az erőforrás ezeket az utasításokat kövesse.

1. Az Azure Portalon keresse meg és jelölje ki **Log Analytics** a bal oldali navigációs menüjében.
2. Keresse meg és válassza ki a engedélyezésekor, diagnostics létrehozott erőforrást.
3. A **általános**, keresse meg és jelölje ki **naplók**. Ezen az oldalon a naplók lekérdezéseket is futtatható.

### <a name="sample-queries"></a>Mintalekérdezések

Az alábbiakban néhány alapvető Kusto lekérdezések segítségével ismerje meg a naplózási adatokat.

Ez a lekérdezés az összes diagnosztikai napló futtatása az Azure Cognitive Services egy megadott időszak:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Tekintse meg a 10 legújabb naplókat a lekérdezés futtatásával:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Ez a lekérdezés futtatása a csoport-műveletek **erőforrás**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
A művelet végrehajtásához szükséges átlagos időt található lekérdezés futtatásával:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

A lekérdezés megtekintése a műveletek mennyisége idővel az összes 10s binned számokkal OperationName felosztás futtatására.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan engedélyezze a naplózást, valamint a különböző Azure-szolgáltatások által támogatott mérőszámokban és naplófájlokban kategóriák, olvassa el mind a [Mérőszámokáttekintése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) Microsoft Azure-ban, és [áttekintése az Azure diagnosztikai naplók ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) cikkeket.
* Olvassa el ezeket a cikkeket az event hubs ismertetése:
  * [Mi az Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Bevezetés az Event Hubs használatába](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Olvasási [metrikák és diagnosztikai naplók letöltése az Azure Storage-ból](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Olvasási [naplókeresések megismerése az Azure Monitor naplóira](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
