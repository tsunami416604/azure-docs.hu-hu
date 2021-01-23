---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742683"
---
## <a name="examples"></a>Példák

### <a name="filter-on-system-properties"></a>Szűrés a rendszer tulajdonságainál
Ha egy szűrő rendszertulajdonságára szeretne hivatkozni, használja a következő formátumot: `sys.<system-property-name>` . 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>Szűrés az üzenet tulajdonságainál
Íme néhány példa az üzenet tulajdonságainak egy szűrőben való használatára. Az üzenet tulajdonságai a vagy a használatával érhetők el `user.property-name` `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>Az üzenet tulajdonságainak szűrése speciális karakterekkel
Ha az üzenet tulajdonságának neve speciális karaktereket tartalmaz, használjon idézőjeleket ( `"` ) a tulajdonság nevének a megadásához. Ha például a tulajdonság neve `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` , használja a következő szintaxist a szűrőben. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>Az üzenet tulajdonságainak szűrése numerikus értékekkel
Az alábbi példák bemutatják, hogyan használhatók a tulajdonságok numerikus értékekkel a szűrőkben. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>Paramétereken alapuló szűrők
Íme néhány példa a paramétereken alapuló szűrők használatára. Ezekben a példákban `DataTimeMp` egy típusú üzenet tulajdonság, amely `DateTime` egy `@dtParam` objektumként a szűrőnek átadott paraméter `DateTime` .

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>Használata a-ben és nem

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

C#-minta esetén tekintse [meg a githubon a témakör szűrők minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)című szakaszát.


### <a name="set-rule-action-for-a-sql-filter"></a>Szabály műveletének beállítása SQL-szűrőhöz

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>Korrelációs szűrő a CorrelationID használatával

```csharp
new CorrelationFilter("Contoso");
```

A beállítással rendelkező üzeneteket szűri `CorrelationID` `Contoso` . 

### <a name="correlation-filter-using-system-and-user-properties"></a>Korrelációs szűrő a rendszer és a felhasználó tulajdonságaival

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

A következővel egyenértékű: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

