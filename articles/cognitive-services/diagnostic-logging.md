---
title: Diagnosztikai naplózás
titleSuffix: Azure Cognitive Services
description: Ez az útmutató lépésenkénti utasításokat tartalmaz az Azure Cognitive Service diagnosztikai naplózásának engedélyezéséhez. Ezek a naplók gazdag, gyakori adatokat szolgáltatnak a probléma azonosításához és hibakereséséhez használt erőforrások működéséről.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "71827904"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Diagnosztikai naplózás engedélyezése az Azure Cognitive Services szolgáltatáshoz

Ez az útmutató lépésenkénti utasításokat tartalmaz az Azure Cognitive Service diagnosztikai naplózásának engedélyezéséhez. Ezek a naplók gazdag, gyakori adatokat szolgáltatnak a probléma azonosításához és hibakereséséhez használt erőforrások működéséről. A folytatás előtt rendelkeznie kell egy Azure-fiókkal, amely legalább egy Cognitive Service-előfizetéssel rendelkezik, például [a Bing Web Search,](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview) [a Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)vagy [a LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)szolgáltatásra.

## <a name="prerequisites"></a>Előfeltételek

A diagnosztikai naplózás engedélyezéséhez valahol a naplóadatok tárolásához kell tárolnia. Ez az oktatóanyag az Azure Storage és a Log Analytics szolgáltatást használja.

* [Azure storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) – megőrzi a házirend-naplózás, a statikus elemzés vagy a biztonsági mentés diagnosztikai naplóit. A tárfiók nem kell ugyanabban az előfizetésben, mint az erőforrás-kibocsátó naplók, amíg a felhasználó, aki konfigurálja a beállítást rendelkezik a megfelelő RBAC-hozzáférés mindkét előfizetéshez.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) – Egy rugalmas naplókeresési és -elemzési eszköz, amely lehetővé teszi az Azure-erőforrások által létrehozott nyers naplók elemzését.

> [!NOTE]
> További konfigurációs lehetőségek állnak rendelkezésre. További információ: [Naplóadatok gyűjtése és felhasználása az Azure-erőforrásokból.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)

## <a name="enable-diagnostic-log-collection"></a>Diagnosztikai naplógyűjtemény engedélyezése  

Kezdjük azzal, hogy engedélyezi a diagnosztikai naplózást az Azure Portalon.

> [!NOTE]
> Ha engedélyezni szeretné ezt a funkciót a PowerShell vagy az Azure CLI használatával, kövesse az [Azure-erőforrásokból származó naplóadatok gyűjtése és felhasználása](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)című részben található utasításokat.

1. Lépjen az Azure Portalra. Ezután keresse meg és válassza ki a Cognitive Services-erőforrást. Például a Bing Web Search-előfizetése.   
2. Ezután a bal oldali navigációs menüben keresse meg a **Figyelés parancsot,** és válassza a **Diagnosztikai beállítások lehetőséget.** Ez a képernyő az erőforrás hoz korábban létrehozott diagnosztikai beállításokat tartalmazza.
3. Ha van egy korábban létrehozott erőforrás, amelyet használni szeretne, most kiválaszthatja azt. Ellenkező esetben válassza **a + Diagnosztikai beállítás hozzáadása lehetőséget**.
4. Adja meg a beállítás nevét. Ezután válassza **az Archiválás egy tárfiókba lehetőséget,** és **a Küldés a naplóba Analytics**lehetőséget.
5. Amikor a rendszer kéri a konfigurálásra, válassza ki a tárfiókot és az OMS-munkaterületet, amelyet a diagnosztikai naplók tárolásához szeretne használni. **Megjegyzés:** Ha nem rendelkezik tárfiókkal vagy OMS-munkaterülettel, kövesse az utasításokat a létrehozáshoz.
6. Válassza **a Naplózás**, **RequestResponse**és **AllMetrics lehetőséget.** Ezután állítsa be a diagnosztikai naplóadatok megőrzési idejét. Ha az adatmegőrzési szabály értéke nulla, az adott naplókategória eseményei határozatlan ideig tárolódnak.
7. Kattintson a **Mentés** gombra.

Az adatok lekérdezéséhez és elemzéséhez akár két órát is igénybe vehet, mielőtt az adatok naplózása lekérdezésre és elemzésre rendelkezésre áll. Szóval ne aggódj, ha nem látsz semmit azonnal.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Diagnosztikai adatok megtekintése és exportálása az Azure Storage szolgáltatásból

Az Azure Storage egy robusztus objektumtárolási megoldás, amely nagy mennyiségű strukturálatlan adatok tárolására van optimalizálva. Ebben a szakaszban megtudhatja, hogy a tárfiók összes tranzakció 30 napos időkereten belül, és exportálja az adatokat excel.

1. Az Azure Portalon keresse meg az Azure Storage-erőforrás, amely az utolsó szakaszban létrehozott.
2. A bal oldali navigációs menüben keresse meg a **Figyelés** parancsot, és válassza **a Mérőszámok lehetőséget.**
3. A lekérdezés konfigurálásához használja a rendelkezésre álló legördülő adatokat. Ebben a példában állítsuk be az időtartományt **az Elmúlt 30 napra,** a metrikát **pedig Tranzakció ra.**
4. Amikor a lekérdezés befejeződött, megjelenik egy vizualizációs tranzakció az elmúlt 30 napban. Az adatok exportálásához használja a lap tetején található **Exportálás Excelbe** gombot.

További információ arról, hogy mi a teendő a diagnosztikai adatokkal az [Azure Storage-ban.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)

## <a name="view-logs-in-log-analytics"></a>Naplók megtekintése a Log Analyticsben

Kövesse ezeket az utasításokat az erőforrás naplóelemzési adatainak megismeréséhez.

1. Az Azure Portalon keresse meg és válassza a **Log Analytics** a bal oldali navigációs menüben.
2. Keresse meg és jelölje ki a diagnosztika engedélyezésekor létrehozott erőforrást.
3. Az **Általános csoportban**keresse meg és válassza **a Naplók lehetőséget.** Ezen az oldalon lekérdezéseket futtathat a naplókon.

### <a name="sample-queries"></a>Mintalekérdezések

Íme néhány alapvető Kusto-lekérdezés, amely a naplóadatok feltárásához használható.

Futtassa ezt a lekérdezést az Azure Cognitive Services összes diagnosztikai naplójának egy meghatározott időszakra vonatkozóan:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Futtassa ezt a lekérdezést a 10 legutóbbi napló megtekintéséhez:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Futtassa ezt a lekérdezést a műveletek erőforrás szerint való **csoportosításához**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Futtassa ezt a lekérdezést a művelet végrehajtásához szükséges átlagos idő megkereséséhez:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Futtassa ezt a lekérdezést az OperationName szerint felosztott műveletek mennyiségének megtekintéséhez, amelynek száma minden 10-esnél binned.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>További lépések

* A naplózás engedélyezésének, valamint a különböző Azure-szolgáltatások által támogatott metrikák és naplókategóriák megismeréséhez olvassa el a [Metrikák áttekintése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) a Microsoft Azure-ban és az Azure diagnosztikai naplók ról szóló cikkek [áttekintése](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) című témakört.
* Az eseményközpontokról az alábbi cikkekben olvashat:
  * [Mi az az Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Bevezetés az Event Hubs használatába](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Olvassa [el a letöltési metrikákat és diagnosztikai naplókat az Azure Storage ból.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)
* Olvassa [el a naplókeresések ismertetése az Azure Monitor naplóiban.](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)
