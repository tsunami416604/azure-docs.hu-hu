---
title: Az Azure Data Explorer betöltési műveleteinek figyelése diagnosztikai naplók használatával
description: Megtudhatja, hogyan állíthat be diagnosztikai naplókat az Azure Data Explorer betöltési műveletek figyelésére.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277433"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Az Azure Data Explorer betöltési műveleteinek figyelése diagnosztikai naplók használatával (előzetes verzió)

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához először egy fürtöt hozunk létre, majd egy vagy több adatbázist a fürtben. Ezután az adatokat egy adatbázis táblájába kell betöltenie (betölteni), így lekérdezéseket futtathat ellene. [Az Azure Monitor diagnosztikai naplói](/azure/azure-monitor/platform/diagnostic-logs-overview) adatokat szolgáltatnak az Azure-erőforrások működéséről. Az Azure Data Explorer diagnosztikai naplókat használ a betöltési sikerességekkel és hibákkal kapcsolatos elemzésekhez. A műveletnaplók at exportálhatja az Azure Storage, az Event Hub vagy a Log Analytics szolgáltatásba a betöltési állapot figyeléséhez. Az Azure Storage és az Azure Event Hub naplói további elemzés céljából átirányíthatók az Azure Data Explorer-fürt egy táblájába.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)
* Hozzon létre [egy fürtöt és adatbázist](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Diagnosztikai naplók beállítása egy Azure Data Explorer-fürthöz

A diagnosztikai naplók a következő naplóadatok gyűjtésének konfigurálására használhatók:
* Sikeres betöltési műveletek: Ezek a naplók a sikeresen befejezett betöltési műveletekadatait tartalmazják.
* Sikertelen betöltési műveletek: Ezek a naplók részletes információkat tartalmaznak a sikertelen betöltési műveletekről, beleértve a hiba részleteit is. 

Az adatokat ezután archiválja egy Tárfiókba, streameli egy Event Hubba, vagy elküldi a Log Analytics-nek, a specifikációknak megfelelően.

### <a name="enable-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése

A diagnosztikai naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez tegye a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)válassza ki az Azure Data Explorer fürterőforrást, amelyet figyelni szeretne.
1. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.
  
    ![Diagnosztikai naplók hozzáadása](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Válassza **a Diagnosztikai beállítás hozzáadása lehetőséget.**
1. A **Diagnosztika beállításai** ablakban:
 
    ![Diagnosztikai beállítások konfigurálása](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Válassza a **Név lehetőséget** a diagnosztikai beállításhoz.
    1. Jelöljön ki egy vagy több célt: egy tárfiókot, az Event Hubot vagy a Log Analytics-et.
    1. Válassza ki az összegyűjtendő naplókat: `SucceededIngestion` vagy `FailedIngestion`a .
    1. Válassza ki a begyűjtendő [mérőszámokat](using-metrics.md#supported-azure-data-explorer-metrics) (nem kötelező).  
    1. Válassza a **Mentés** lehetőséget az új diagnosztikai naplók beállításainak és metrikáinak mentéséhez.
    1. Hozzon létre egy **új támogatási kérelmet** az Azure Portalon a diagnosztikai naplók aktiválásának kéréséhez.

Az új beállítások néhány perc múlva beállíthatók. A naplók ezután megjelennek a konfigurált archiválási célban (tárfiók, Event Hub vagy Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók sémája

Az [Azure Monitor összes diagnosztikai naplója közös legfelső szintű sémával rendelkezik.](/azure/azure-monitor/platform/diagnostic-logs-schema) Az Azure Data Explorer egyedi tulajdonságokkal rendelkezik a saját események. Minden napló JSON formátumban van tárolva.

### <a name="ingestion-logs-schema"></a>Betöltési naplók sémája

A napló JSON-karakterláncai az alábbi táblázatban felsorolt elemeket tartalmaznak:

|Név               |Leírás
|---                |---
|time               |A jelentés időpontja
|resourceId         |Az Azure Resource Manager erőforrásazonosítója
|operationName      |A művelet neve: 'MICROSOFT. KUSTO/CLUSTERS/BETEJ/CSELEKVÉS"
|operationVersion   |Sémaverzió: '1.0' 
|category           |A művelet kategóriája. `SucceededIngestion` vagy `FailedIngestion`. A tulajdonságok különböznek a sikeres vagy [sikertelen művelethez.](#failed-ingestion-operation-log) [successful operation](#successful-ingestion-operation-log)
|properties         |Részletes információk a műveletről.

#### <a name="successful-ingestion-operation-log"></a>Sikeres betöltési műveletnapló

**Példa:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**A sikeres műveletdiagnosztikai napló tulajdonságai**

|Név               |Leírás
|---                |---
|succeededOn        |A lenyelés befejezésének időpontja
|operationId        |Az Azure Data Explorer betöltési műveletazonosítója
|adatbázis           |A céladatbázis neve
|tábla              |A céltábla neve
|lenyelésForrásazonosító  |A betöltési adatforrás azonosítója
|betöltésforrás-vezérlő|A betöltési adatforrás vagy blob URI elérési útja
|rootActivityId     |Tevékenységazonosító

#### <a name="failed-ingestion-operation-log"></a>Sikertelen betöltési művelet naplója

**Példa:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**A sikertelen műveletdiagnosztikai napló tulajdonságai**

|Név               |Leírás
|---                |---
|nem sikerültOn           |A lenyelés befejezésének időpontja
|operationId        |Az Azure Data Explorer betöltési műveletazonosítója
|adatbázis           |A céladatbázis neve
|tábla              |A céltábla neve
|lenyelésForrásazonosító  |A betöltési adatforrás azonosítója
|betöltésforrás-vezérlő|A betöltési adatforrás vagy blob URI elérési útja
|rootActivityId     |Tevékenységazonosító
|Részletek            |A hiba és a hibaüzenet részletes leírása
|hibakód          |Hibakód 
|hibaÁllapota      |`Permanent` vagy `Transient`. Egy átmeneti hiba újrapróbálkozása sikeres lehet.
|originatesFromUpdatePolicy|Igaz, ha a hiba frissítési házirendből származik
|kellRetry        |Igaz, ha az újrapróbálkozás sikeres lehet

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Adatok betöltése és lekérdezésfigyelési adatok betöltése az Azure Data Explorerben](ingest-data-no-code.md)
* [Metrikák használata a fürtállapot monitorozásához](using-metrics.md)

