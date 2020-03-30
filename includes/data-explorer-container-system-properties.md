---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128752"
---
### <a name="event-system-properties-mapping"></a>Eseményrendszer tulajdonságainak leképezése

> [!Note]
> * A rendszertulajdonságok egyrekordú események esetén támogatottak.
> * A `csv` leképezéshez a rendszer a rekord elején adja hozzá a tulajdonságokat. A `json` leképezéshez a tulajdonságok a legördülő listában megjelenő névnek megfelelően kerülnek hozzáadásra.

Ha a tábla **Adatforrás** szakaszában az **Eseményrendszer tulajdonságai** lehetőséget választotta, a következő tulajdonságokat kell szerepelnie a táblasémában és a leképezésben.

**Példa táblasémára**

Ha az adatok három`Timespan` `Metric`oszlopot `Value`( , és `x-opt-enqueued-time` ) `x-opt-offset`tartalmaznak, és a benne lévő tulajdonságok a következők, és a, a táblaséma létrehozása vagy módosítása a következő paranccsal:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Példa CSV-leképezésre**

Futtassa a következő parancsokat, hogy adatokat adjon a rekord elejéhez. Megjegyzés: a csoportértékek.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Példa JSON-leképezésre**

Az adatok a rendszertulajdonságok neveinek használatával kerülnek hozzáadásra, ahogy azok megjelennek az **Adatkapcsolat** panel **Eseményrendszer tulajdonságai** listájában. Futtassa a következő parancsokat:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
