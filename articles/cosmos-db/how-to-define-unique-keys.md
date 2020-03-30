---
title: Egyedi kulcsok definiálása egy Azure Cosmos-tárolóhoz
description: Ismerje meg, hogyan definiálhat egyedi kulcsokat egy Azure Cosmos-tárolóhoz az Azure Portal, a PowerShell, a .Net, a Java és számos más SDK használatával.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: fa62495a7b51c9a06a91102299378c15e811eae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872111"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Egyedi kulcsok definiálása egy Azure Cosmos-tárolóhoz

Ez a cikk bemutatja az egyedi kulcsok definiálásának különböző [módjait](unique-keys.md) az Azure Cosmos-tároló létrehozásakor. Jelenleg az Azure Portalon vagy az SDK-k egyikén keresztül hajthatja végre ezt a műveletet.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-sql-api-dotnet.md#create-account) vagy válasszon ki egy meglévőt.

1. Nyissa meg az **Adatkezelő** ablaktáblát, és jelölje ki azt a tárolót, amelyen dolgozni szeretne.

1. Kattintson az **Új tároló gombra.**

1. A **Tároló hozzáadása** párbeszédpanelen kattintson a **+ Egyedi kulcs hozzáadása** gombra egy egyedi kulcsbejegyzés hozzáadásához.

1. Az egyedi kulcsmegkötés elérési útjának megadása

1. Ha szükséges, adjon hozzá több egyedi kulcs bejegyzések kattintva **+ Egyedi kulcs hozzáadása**

    ![Képernyőkép az Azure Portalon található egyedi kulcsmegkötési bejegyzésről](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-powershell"></a>Powershell használata

Hozzon létre egy tárolót egyedi kulcsokkal [lásd: Hozzon létre egy Azure Cosmos-tároló egyedi kulccsal és TTL](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk-v2"></a>A .NET SDK V2 használata

Új tároló létrehozásakor a [.NET SDK v2 használatával](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)egy `UniqueKeyPolicy` objektum használható egyedi kulcsmegkötések definiálásához.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-net-sdk-v3"></a>A .NET SDK V3 használata

Új tároló létrehozásakor a [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)használatával, használja az SDK gördülékeny API-t az egyedi kulcsok tömör és olvasható módon deklarálása.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>A Java SDK használata

Új tároló létrehozásakor a [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)használatával egy `UniqueKeyPolicy` objektum használható egyedi kulcsmegkötések definiálására.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>A Node.js SDK használata

Új tároló létrehozásakor a [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos)használatával egy `UniqueKeyPolicy` objektum használható egyedi kulcsmegkötések definiálásához.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>A Python SDK használata

Amikor egy új tárolót hoz létre a [Python SDK](https://pypi.org/project/azure-cosmos/)használatával, egyedi kulcsmegkötések adhatók meg a paraméterként megadott szótár részeként.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>További lépések

- További információ a [particionálásról](partition-data.md)
- Az [indexelés működésének megismerése](index-overview.md)
