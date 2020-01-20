---
title: Azure Adatkezelő-betöltési műveletek figyelése diagnosztikai naplók használatával
description: Ismerje meg, hogyan állíthatja be az Azure Adatkezelő diagnosztikai naplóit a betöltési műveletek figyeléséhez.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277433"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Azure Adatkezelő-betöltési műveletek figyelése diagnosztikai naplók használatával (előzetes verzió)

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet alkalmazások, webhelyek, IoT-eszközök és egyebek nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti a (betöltés) típusú adatot egy adatbázisba, így lekérdezéseket futtathat. [Azure monitor diagnosztikai naplók](/azure/azure-monitor/platform/diagnostic-logs-overview) az Azure-erőforrások működésével kapcsolatos információkat biztosítanak. Az Azure Adatkezelő diagnosztikai naplókat használ a betöltési sikerek és hibák elemzéséhez. A műveleti naplókat az Azure Storage-ba, az Event Hubbe vagy a Log Analyticsba exportálhatja a betöltési állapot figyeléséhez. Az Azure Storage-ból és az Azure Event hub-ból származó naplók további elemzés céljából átirányíthatók az Azure Adatkezelő-fürt egyik táblájába.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/).
* Hozzon létre egy [fürtöt és egy adatbázist](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Diagnosztikai naplók beállítása Azure Adatkezelő-fürthöz

A diagnosztikai naplók használatával konfigurálhatja a következő naplózási adatkészletek gyűjteményét:
* Sikeres betöltési műveletek: ezek a naplók információkkal szolgálnak a sikeresen befejezett betöltési műveletekről.
* Sikertelen betöltési műveletek: ezek a naplók részletes információkat tartalmaznak a sikertelen betöltési műveletekről, beleértve a hibák részleteit. 

Az adatok ezután archiválva lesznek egy Storage-fiókba, egy Event hub streambe áramlanak, vagy az Ön által megadott specifikációknak megfelelően a Log Analyticsba lesznek küldve.

### <a name="enable-diagnostic-logs"></a>Diagnosztikai naplók engedélyezése

A diagnosztikai naplók alapértelmezés szerint le vannak tiltva. A diagnosztikai naplók engedélyezéséhez hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza ki a figyelni kívánt Azure adatkezelő fürterőforrás-erőforrást.
1. A **figyelés**területen válassza a **diagnosztikai beállítások**elemet.
  
    ![Diagnosztikai naplók hozzáadása](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Válassza a **diagnosztikai beállítás hozzáadása**lehetőséget.
1. A **diagnosztikai beállítások** ablakban:
 
    ![Diagnosztikai beállítások konfigurálása](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Válassza ki a diagnosztikai beállítás **nevét** .
    1. Válasszon ki egy vagy több célt: egy Storage-fiókot, egy Event hubot vagy egy Log Analytics.
    1. Válassza ki a gyűjteni kívánt naplókat: `SucceededIngestion` vagy `FailedIngestion`.
    1. Válassza ki a gyűjteni kívánt [metrikákat](using-metrics.md#supported-azure-data-explorer-metrics) (nem kötelező).  
    1. Válassza a **Mentés** lehetőséget az új diagnosztikai naplók beállításainak és metrikáinak mentéséhez.
    1. Hozzon létre egy **új támogatási kérelmet** a Azure Portal a diagnosztikai naplók aktiválásának kérelmezéséhez.

Néhány perc múlva új beállítások lesznek beállítva. A naplók ezután megjelennek a konfigurált archiválási tárolóban (Storage-fiók, Event hub vagy Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Diagnosztikai naplók sémája

Az összes [Azure monitor diagnosztikai napló közös legfelső szintű sémával rendelkezik](/azure/azure-monitor/platform/diagnostic-logs-schema). Az Azure Adatkezelő saját eseményeihez egyedi tulajdonságokkal rendelkezik. Az összes napló JSON formátumban van tárolva.

### <a name="ingestion-logs-schema"></a>Betöltési naplók sémája

A JSON-karakterláncok naplózása a következő táblázatban felsorolt elemeket tartalmazza:

|Name (Név)               |Leírás
|---                |---
|time               |A jelentés időpontja
|resourceId         |Erőforrás-azonosító Azure Resource Manager
|operationName      |A művelet neve: "MICROSOFT. KUSTO/FÜRTÖK/BETÖLTÉS/MŰVELET
|operationVersion   |Séma verziója: "1,0" 
|category           |A művelet kategóriája. `SucceededIngestion` vagy `FailedIngestion`. A tulajdonságok a [sikeres művelet](#successful-ingestion-operation-log) vagy a [sikertelen művelet](#failed-ingestion-operation-log)esetében különböznek.
|properties         |A művelet részletes információi.

#### <a name="successful-ingestion-operation-log"></a>Sikeres betöltési műveleti napló

**Példa**

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
**A sikeres művelet diagnosztikai naplójának tulajdonságai**

|Name (Név)               |Leírás
|---                |---
|succeededOn        |Betöltési idő
|operationId        |Azure Adatkezelő betöltési művelet azonosítója
|adatbázis           |A céladatbázis neve
|table              |A céltábla neve
|ingestionSourceId  |A betöltési adatforrás azonosítója
|ingestionSourcePath|A betöltési adatforrás vagy a blob URI elérési útja
|rootActivityId     |Tevékenységazonosító

#### <a name="failed-ingestion-operation-log"></a>Sikertelen betöltési műveleti napló

**Példa**

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

**Sikertelen művelet diagnosztikai naplójának tulajdonságai**

|Name (Név)               |Leírás
|---                |---
|failedOn           |Betöltési idő
|operationId        |Azure Adatkezelő betöltési művelet azonosítója
|adatbázis           |A céladatbázis neve
|table              |A céltábla neve
|ingestionSourceId  |A betöltési adatforrás azonosítója
|ingestionSourcePath|A betöltési adatforrás vagy a blob URI elérési útja
|rootActivityId     |Tevékenységazonosító
|Részletek            |A hiba és a hibaüzenet részletes leírása
|errorCode          |Hibakód 
|failureStatus      |`Permanent` vagy `Transient`. Egy átmeneti hiba esetén az Újrapróbálkozás sikeres lehet.
|originatesFromUpdatePolicy|Igaz, ha a hiba egy frissítési házirendből származik
|shouldRetry        |Igaz, ha az Újrapróbálkozás sikeres lehet

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: figyelési adatfeldolgozás és-lekérdezés az Azure Adatkezelő](ingest-data-no-code.md)
* [Metrikák használata a fürt állapotának figyeléséhez](using-metrics.md)

