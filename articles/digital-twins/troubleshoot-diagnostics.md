---
title: Diagnosztika beállítása
titleSuffix: Azure Digital Twins
description: 'Lásd: a naplózás engedélyezése a diagnosztikai beállításokkal.'
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: fc397b6d6beb719e11dc3959bbcf4d75c08a8dda
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723928"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins hibaelhárítása: diagnosztika naplózása

Az Azure Digital Twins a szolgáltatási példány [metrikáit](troubleshoot-metrics.md) gyűjti, amelyek információkat biztosítanak az erőforrások állapotáról. Ezeket a mérőszámokat felhasználhatja az Azure Digital Twins szolgáltatás általános állapotának felmérésére és a hozzájuk kapcsolódó erőforrásokra. Ezek a felhasználók felé irányuló statisztikák segítenek megtekinteni, hogy mi történik az Azure-beli digitális Ikrekben, és segítsen a problémák kiváltó okának elemzésében anélkül, hogy kapcsolatba kellene lépnie az Azure ügyfélszolgálatával.

Ez a cikk bemutatja, hogyan kapcsolhatja be a **diagnosztikai naplózást** a metrikák adataihoz az Azure Digital Twins-példányból. Ezeket a naplókat használhatja a szolgáltatással kapcsolatos problémák elhárításához, valamint a diagnosztikai beállítások konfigurálásához, hogy az Azure Digital Twins-metrikákat különböző célhelyekre küldje. Ezekről a beállításokról további információt a [*diagnosztikai beállítások létrehozása a platform-naplók és a metrikák különböző célhelyekre való küldéséhez*](../azure-monitor/platform/diagnostic-settings.md)című cikkből kaphat.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Diagnosztikai beállítások bekapcsolása a Azure Portal

Az alábbi módon engedélyezheti az Azure Digital Twins-példány diagnosztikai beállításait:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure Digital Twins-példányhoz. A nevét a portál keresési sávjába írja be. 

2. Válassza a menüből a **diagnosztikai beállítások** elemet, majd **adja hozzá a diagnosztikai**beállításokat.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="A diagnosztikai beállítások lapot és a hozzáadni kívánt gombot ábrázoló képernyőkép":::

3. Az alábbi lapon adja meg a következő értékeket:
     * **Diagnosztikai beállítás neve**: adjon meg egy nevet a diagnosztikai beállításoknak.
     * **Kategória részletei**: válassza ki a figyelni kívánt műveleteket, majd jelölje be a jelölőnégyzeteket a diagnosztika engedélyezéséhez a műveletekhez. A diagnosztikai beállítások által a következő műveleteket lehet jelenteni:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        További információ ezekről a beállításokról: [*Kategória részletei*](#category-details) szakasz.
     * **Célhely részletei**: válassza ki, hová szeretné elküldeni a naplókat. A három lehetőség bármely kombinációját kiválaszthatja:
        - Küldés a Log Analyticsnek
        - Archiválás tárfiókba
        - Streamelés eseményközpontba

        Előfordulhat, hogy további részleteket is meg kell adnia, ha azok a célhely kiválasztásához szükségesek.  
    
4. Mentse az új beállításokat. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Képernyőfelvétel: a diagnosztikai beállítások oldal, ahol a felhasználó kitöltötte a diagnosztikai beállítások nevét, és a kategória részletei és a célhely részletei jelölőnégyzettel rendelkezik. A Mentés gomb ki van emelve.":::

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezután a naplók megjelennek a konfigurált célhelyen a példány **diagnosztikai beállítások** lapján. 

## <a name="category-details"></a>Kategória részletei

A diagnosztikai beállítások beállításakor a **Kategória részletei** szakaszban kiválaszthatja a naplózási kategóriák részleteit.

| Naplókategória | Leírás |
| --- | --- |
| ADTModelsOperation | A modellekre vonatkozó összes API-hívás naplózása |
| ADTQueryOperation | A lekérdezésekre vonatkozó összes API-hívás naplózása |
| ADTEventRoutesOperation | Naplózza az esemény-útvonalakra vonatkozó összes API-hívást, valamint az Azure Digital Twins-ból érkező eseményeket egy olyan végponti szolgáltatáshoz, mint például a Event Grid, a Event Hubs és a Service Bus |
| ADTDigitalTwinsOperation | Az Azure digitális Twins-hoz tartozó összes API-hívás naplózása |

Minden naplózási kategória írási, olvasási, törlési és művelet-műveletből áll.  Ezek a leképezések a következőképpen REST API hívásokat:

| Eseménytípus | REST API műveletek |
| --- | --- |
| Írás | PUT és PATCH |
| Olvasás | GET |
| Törlés | DELETE |
| Műveletek | POST |

Itt látható a műveletek és a megfelelő [Azure digitális Twins REST API](https://docs.microsoft.com/rest/api/azure-digitaltwins/) a különböző kategóriákba bejelentkezett hívások részletes listája. 

>[!NOTE]
> Minden naplózási kategória több művelet/REST API hívást tartalmaz. Az alábbi táblázatban minden naplózási kategória az összes művelet/REST API hívására vonatkozik, amíg meg nem jelenik a következő napló kategóriája. 

| Naplókategória | Művelet | Hívások és egyéb események REST API |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modellek/írás | Digital Twin models frissítési API |
|  | Microsoft. DigitalTwins/modellek/olvasás | Az ID és a List API-k által lekérdezett digitális kettős modellek |
|  | Microsoft. DigitalTwins/models/delete | Digital Twin models – API törlése |
|  | Microsoft. DigitalTwins/modellek/művelet | Digitális kettős modellek API hozzáadása |
| ADTQueryOperation | Microsoft. DigitalTwins/lekérdezés/művelet | Az ikrek API lekérdezése |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Event Routes API hozzáadása |
|  | Microsoft. DigitalTwins/eventroutes/READ | AZONOSÍTÓ és lista API-k által beolvasott esemény-útvonalak |
|  | Microsoft. DigitalTwins/eventroutes/delete | Esemény-útvonalak törlése API |
|  | Microsoft. DigitalTwins/eventroutes/művelet | Hiba történt az események végponti szolgáltatásba való közzétételekor (nem API-hívás) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digitális ikrek hozzáadása, kapcsolat hozzáadása, frissítés, összetevő frissítése |
|  | Microsoft. DigitalTwins/DigitalTwins/READ | Digitális ikrek által beolvasott azonosító, összetevő beolvasása, kapcsolat beolvasása azonosító alapján, bejövő kapcsolatok listázása, kapcsolatok listázása |
|  | Microsoft. DigitalTwins/DigitalTwins/delete | Digitális ikrek törlése, kapcsolat törlése |
|  | Microsoft. DigitalTwins/DigitalTwins/művelet | Digitális Twins – összetevő telemetria küldése, telemetria küldése |

## <a name="log-schemas"></a>Naplózási sémák 

Minden naplózási kategória tartalmaz egy sémát, amely meghatározza, hogyan történik az adott kategóriában lévő események jelentése. Minden egyes naplóbejegyzés szövegként tárolódik, és JSON-blobként van formázva. A log és a example JSON-törzs mezői az alábbi naplókhoz vannak megadva. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` és `ADTQueryOperation` egy konzisztens API log-sémát használ, `ADTEventRoutesOperation` a saját külön sémával rendelkezik.

### <a name="api-log-schemas"></a>API-naplózási sémák

Ez a naplózási séma konzisztens a `ADTDigitalTwinsOperation` , és rendszerhez `ADTModelsOperation` `ADTQueryOperation` . Az Azure Digital Twins-példány API-hívásainak információit tartalmazza.

Az API-naplók mező-és tulajdonság-leírása itt található.

| Mező neve | Adattípus | Leírás |
|-----|------|-------------|
| `Time` | DateTime | Az esemény bekövetkezett dátuma és időpontja (UTC) |
| `ResourceID` | Sztring | Annak az erőforrásnak a Azure Resource Manager erőforrás-azonosítója, amelyben az esemény lezajlott |
| `OperationName` | Sztring  | Az esemény során végrehajtandó művelet típusa |
| `OperationVersion` | Sztring | Az esemény során használt API-verzió |
| `Category` | Sztring | A kibocsátott erőforrás típusa |
| `ResultType` | Sztring | Az esemény eredménye |
| `ResultSignature` | Sztring | Az eseményhez tartozó http-állapotkód |
| `ResultDescription` | Sztring | További információk az eseményről |
| `DurationMs` | Sztring | Mennyi ideig tartott az esemény végrehajtása ezredmásodpercben |
| `CallerIpAddress` | Sztring | Az esemény maszkolt forrás IP-címe |
| `CorrelationId` | Guid | Az ügyfél egyedi azonosítót adott meg az eseményhez |
| `Level` | Sztring | Az esemény naplózási súlyossága |
| `Location` | Sztring | Az a régió, ahol a rendezvény lezajlott |
| `RequestUri` | URI | Az esemény során használt végpont |

Az alábbi példa JSON-törzseket mutat be az ilyen típusú naplókhoz.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-05-31-preview",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-05-31-preview"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-05-31-preview",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-05-31-preview",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-05-31-preview",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-05-31-preview",
}
```

### <a name="egress-log-schemas"></a>Kimenő naplózási sémák

Ez a naplók sémája `ADTEventRoutesOperation` . Ezek a kivételekre és az Azure Digital Twins-példányhoz csatlakozó kimenő végpontokra vonatkozó API-műveletekre vonatkozó részleteket tartalmaznak.

|Mező neve | Adattípus | Leírás |
|-----|------|-------------|
| `Time` | DateTime | Az esemény bekövetkezett dátuma és időpontja (UTC) |
| `ResourceId` | Sztring | Annak az erőforrásnak a Azure Resource Manager erőforrás-azonosítója, amelyben az esemény lezajlott |
| `OperationName` | Sztring  | Az esemény során végrehajtandó művelet típusa |
| `Category` | Sztring | A kibocsátott erőforrás típusa |
| `ResultDescription` | Sztring | További információk az eseményről |
| `Level` | Sztring | Az esemény naplózási súlyossága |
| `Location` | Sztring | Az a régió, ahol a rendezvény lezajlott |
| `EndpointName` | Sztring | Az Azure Digital Ikrekben létrehozott kimenő forgalom végpontjának neve |

Az alábbi példa JSON-törzseket mutat be az ilyen típusú naplókhoz.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="next-steps"></a>További lépések

* A diagnosztika konfigurálásával kapcsolatos további információkért lásd: [*adatok gyűjtése és felhasználása az Azure-erőforrásokból*](../azure-monitor/platform/platform-logs-overview.md).
* További információ az Azure digitális Twins metrikáinak használatáról [*: a metrikák megtekintése Azure Monitorsal*](troubleshoot-metrics.md).
* Ha szeretné megtudni, hogyan engedélyezheti a riasztásokat a metrikák számára, tekintse meg a [*Hibaelhárítás: riasztások beállítása*](troubleshoot-alerts.md)című témakört.
