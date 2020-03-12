---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128752"
---
### <a name="event-system-properties-mapping"></a>Eseményrendszer tulajdonságai leképezése

> [!Note]
> * A Rendszertulajdonságok Egyrekordos események esetén támogatottak.
> * `csv` leképezés esetén a tulajdonságok a rekord elején lesznek hozzáadva. `json` leképezés esetén a tulajdonságok a legördülő listában megjelenő név szerint lesznek hozzáadva.

Ha a tábla **adatforrás** szakaszában az **eseményrendszer tulajdonságai** lehetőséget választotta, a táblázat sémájában és a leképezésben a következő tulajdonságokat kell tartalmaznia.

**Példa táblázatos sémára**

Ha az adatai három oszlopot tartalmaznak (`Timespan`, `Metric`és `Value`), és a benne foglalt tulajdonságok `x-opt-enqueued-time` és `x-opt-offset`, a parancs használatával hozza létre vagy módosítsa a tábla sémáját:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Példa CSV-megfeleltetésre**

Az alábbi parancsok futtatásával adja hozzá az adatokhoz a rekord elejét. Megjegyzés sorszámai

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
 
**Példa JSON-megfeleltetésre**

A rendszer a Rendszertulajdonságok neveivel adja hozzá az adatforrásokat, ahogy azok megjelennek az **adatkapcsolat** panel **eseményrendszer tulajdonságai** listájában. Futtassa a következő parancsokat:

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
