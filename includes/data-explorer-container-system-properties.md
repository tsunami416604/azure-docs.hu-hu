---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779950"
---
### <a name="event-system-properties-mapping"></a>Eseményrendszer tulajdonságai leképezése

Ha a fenti táblázat **adatforrás** szakaszában az **eseményrendszer tulajdonságai** lehetőséget választotta, lépjen a [webes felhasználói felületre](https://dataexplorer.azure.com/) a megfelelő leképezés létrehozásához a megfelelő KQL parancs futtatásához.

   **CSV-megfeleltetéshez:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **JSON-megfeleltetés esetén:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * Az összes kijelölt tulajdonságot meg kell adnia a leképezésben. 
   > * A tulajdonságok sorrendje fontos a CSV-leképezésben. A rendszertulajdonságokat az összes többi tulajdonság előtt fel kell tüntetni, és ugyanabban a sorrendben kell szerepelniük az **eseményrendszer tulajdonságai** legördülő listában.