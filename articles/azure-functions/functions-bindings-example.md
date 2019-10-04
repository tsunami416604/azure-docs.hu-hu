---
title: Azure Functions trigger és kötési példa
description: Ismerje meg az Azure Function-kötések konfigurálását
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d1959792823e04cf34d65ab775ae8c51e741e293
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097470"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Functions trigger és kötési példa

Ez a cikk bemutatja, hogyan konfigurálhat egy [triggert és kötést](./functions-triggers-bindings.md) egy Azure-függvényben.

Tegyük fel, hogy új sort szeretne írni az Azure Table Storage-ba, amikor új üzenet jelenik meg az Azure üzenetsor-tárolóban. Ez a forgatókönyv egy Azure üzenetsor-tárolási trigger és egy Azure Table Storage-beli kimeneti kötés használatával valósítható meg. 

Ehhez a forgatókönyvhöz a *function. JSON* fájl szükséges. 

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

A `bindings` tömb első eleme a várólista-tároló triggere. A `type` és`direction` a tulajdonságok azonosítják az triggert. A `name` tulajdonság azonosítja az üzenetsor-üzenet tartalmát fogadó Function paramétert. A figyelni `queueName`kívánt várólista neve, a kapcsolódási karakterlánc pedig a által `connection`azonosított alkalmazás-beállításban található.

A `bindings` tömb második eleme az Azure Table Storage output kötés. A `type` és`direction` a tulajdonság azonosítja a kötést. A `name` tulajdonság azt határozza meg, hogy a függvény hogyan adja meg az új tábla sorát, ebben az esetben a függvény visszatérési értékét használva. A tábla `tableName`neve a (z) helyen található, és a kapcsolatok karakterlánca a által `connection`azonosított alkalmazás-beállításban szerepel.

Ha szeretné megtekinteni és szerkeszteni a *function. JSON* tartalmát a Azure Portalban, kattintson a **speciális szerkesztő** lehetőségre a függvény **integrálás** lapján.

> [!NOTE]
> A értéke `connection` egy olyan Alkalmazásbeállítás neve, amely tartalmazza a kapcsolatok karakterláncát, nem maga a kapcsolatok karakterlánca. A kötések az Alkalmazásbeállítások között tárolt kapcsolati karakterláncokat használják, hogy kikényszerítsék a *function. JSON* nem tartalmaz szolgáltatási titkokat.

## <a name="c-script-example"></a>C#parancsfájl – példa

Az alábbi C# szkript-kód ezzel az triggerrel és kötéssel működik. Figyelje meg, hogy a várólista-üzenet tartalmát `order`megadó paraméter neve; erre a névre van szükség, mert a `name` *function. JSON* tulajdonság értéke`order` 

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

## <a name="javascript-example"></a>JavaScript-példa

Ugyanez a *function. JSON* fájl JavaScript-függvénnyel is használható:

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

## <a name="class-library-example"></a>Példa az osztály könyvtárára

Egy osztály-függvénytárban ugyanazokat az trigger-és &mdash; kötési információs várólistákat és táblázatokat, a Storage-fiókokat, &mdash; a bemeneti és kimeneti függvények paramétereit a function. JSON fájl helyett attribútumok szolgáltatják. Például:

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

Most már van egy működő funkciója, amelyet egy Azure-üzenetsor aktivál, és az adatokat az Azure Table Storage-ba exportálja.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions kötési kifejezés mintái](./functions-bindings-expressions-patterns.md)
