---
title: Azure Functions eseményindítót és a kötési – példa
description: Ismerje meg, hogyan konfigurálhatja az Azure Functions kötések
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 6d5f9b171a4efc5e52d281655de143ac9d40d437
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739594"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Functions eseményindítót és a kötési – példa

Ez a cikk bemutatja, hogyan konfigurálhatja a [triggerét és kötéseit](./functions-triggers-bindings.md) az Azure-függvény.

Tegyük fel, hogy szeretne írni az Azure Table storage egy új sor, amikor egy új üzenet jelenik meg az Azure Queue storage-ban. Ez a forgatókönyv segítségével valósítható meg az Azure Queue storage-eseményindító és az Azure Table storage kimeneti kötést. 

Íme egy *function.json* fájl ebben a forgatókönyvben. 

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

Az első eleme a `bindings` a Queue storage eseményindítója. A `type` és `direction` tulajdonságok az eseményindító azonosítására. A `name` tulajdonság azonosítja a függvény paraméter, amely megkapja a várólista üzenet tartalma. Figyelni kívánt üzenetsor neve `queueName`, és a kapcsolati karakterlánc által azonosított Alkalmazásbeállítás `connection`.

A második eleme a `bindings` az Azure Table Storage kimeneti kötést. A `type` és `direction` tulajdonságok a kötés azonosítására. A `name` tulajdonság határozza meg, hogyan nyújt az a függvény a az új táblázat sorban, ebben az esetben a függvény használatával ad vissza értéket. A tábla neve `tableName`, és a kapcsolati karakterlánc által azonosított Alkalmazásbeállítás `connection`.

Megtekintheti és szerkesztheti a tartalmát *function.json* az Azure Portalon kattintson a **speciális szerkesztő** beállítást a **integráció** a függvény lapon.

> [!NOTE]
> Az érték `connection` a kapcsolati karakterláncot, nem pedig magát a kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Kötések használata a kapcsolati karakterláncokat tárolja az alkalmazásbeállítások kényszeríteni a legjobb megoldás, amely *function.json* szolgáltatás titkos kulcsok nem tartalmaz.

## <a name="c-script-example"></a>C#-szkript példa

Itt látható C# parancsfájl-kód a trigger és a kötés. Figyelje meg, hogy a paraméter, amely biztosítja az üzenetsor üzenet tartalmának neve `order`; ez nevét kötelező megadni, mert a `name` tulajdonságértéket *function.json* van `order` 

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

Azonos *function.json* fájl JavaScript-függvény használható:

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

## <a name="class-library-example"></a>Osztály könyvtár példa

Egy osztálytár, a ugyanaz az eseményindító és a kötési információ &mdash; üzenetsor és a táblázat neve, a storage-fiókok esetében függvény bemeneti és kimeneti paramétereinek &mdash; helyett egy function.json fájlt attribútumok által biztosított. Például:

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

Most már működő Azure Table storage, amely az adatokat egy üzenetsor által aktivált függvény.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Functions kötés kifejezés minták](./functions-bindings-expressions-patterns.md)