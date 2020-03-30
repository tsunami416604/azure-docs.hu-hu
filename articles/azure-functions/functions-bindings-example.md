---
title: Az Azure Functions eseményindító és kötési példa
description: Az Azure-függvénykötések konfigurálásának elsajátítása
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227244"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Az Azure Functions eseményindító és kötési példa

Ez a cikk bemutatja, hogyan konfigurálhat egy [eseményindítót és kötéseket](./functions-triggers-bindings.md) egy Azure-függvényben.

Tegyük fel, hogy szeretne írni egy új sort az Azure Table storage-ba, ha egy új üzenet jelenik meg az Azure Queue storage.T t you want to write a new sort to Azure Table storage whenever a new message appears in Azure Queue storage. Ez a működés megvalósítható egy Azure Queue-tárolóbeli eseményindítóval, és egy Azure Table-tárolóbeli kimeneti kötéssel. 

Íme egy *function.json* fájl ehhez a forgatókönyvhöz. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

A tömb első `bindings` eleme a Várólista tárolási eseményindítója. A `type` `direction` és a tulajdonságok azonosítják az eseményindítót. A `name` tulajdonság azonosítja a várólistaüzenet tartalmát fogadó függvényparamétert. A figyelni a napló neve `queueName`a alkalmazásban van, a kapcsolati `connection`karakterlánc pedig a. által azonosított alkalmazásbeállításban.

A tömb második `bindings` eleme az Azure Table Storage kimeneti kötés. A `type` `direction` tulajdonságok azonosítják a kötést. A `name` tulajdonság azt határozza meg, hogy a függvény hogyan adja meg az új táblasort, ebben az esetben a függvény visszatérési értékének használatával. A tábla neve a `tableName`alkalmazásban található, a kapcsolati karakterlánc `connection`pedig a. által azonosított alkalmazásbeállításban.

A *function.json* tartalmának megtekintéséhez és szerkesztéséhez az Azure Portalon kattintson a **Speciális szerkesztő** lehetőségre a funkció **Integrálás** lapján.

> [!NOTE]
> Az érték `connection` egy olyan alkalmazásbeállítás neve, amely a kapcsolati karakterláncot tartalmazza, nem maga a kapcsolati karakterlánc. A kötések az alkalmazásbeállításokban tárolt kapcsolati karakterláncokat használják az ajánlott eljárás érvényesítéséhez, amelyet a *function.json* nem tartalmaz szolgáltatástitokban.

## <a name="c-script-example"></a>Például C# parancsfájl

Itt van a C# parancsfájlkód, amely ezzel az eseményindítóval és kötéssel működik. Figyelje meg, hogy a várólistaüzenet `order`tartalmát szolgáltató paraméter neve ; ez a név `name` azért szükséges, mert a *function.json* tulajdonságértéke`order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Példa JavaScript-re

Ugyanaz a *function.json* fájl javascript függvénnyel használható:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Példa osztálytárra

Az osztálytárakban ugyanazt az &mdash; eseményindító- és kötési információs várólistát &mdash; és táblaneveket, tárfiókokat, bemeneti és kimeneti függvényparamétereket a function.json fájl helyett attribútumok biztosítják. Például:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Most már rendelkezik egy működő függvény, amely egy Azure-várólista által aktivált és az adatok kimenete az Azure Table storage.You now have a working function that is triggered by an Azure Queue and outputs data to Azure Table storage.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Functions kötési kifejezésmintái](./functions-bindings-expressions-patterns.md)
