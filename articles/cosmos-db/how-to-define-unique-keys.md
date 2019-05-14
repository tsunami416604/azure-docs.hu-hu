---
title: Egy Azure Cosmos-tárolóhoz egyedi kulcsok definiálása
description: Ismerje meg, hogyan adhat meg egy Azure Cosmos-tárolóhoz egyedi kulcsok
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 5/3/2019
ms.author: thweiss
ms.openlocfilehash: 1e8a88b640d588fbdb96089f11672b09590ee5d9
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597728"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Egy Azure Cosmos-tárolóhoz egyedi kulcsok definiálása

Ez a cikk bemutatja a különböző módszerrel határozhatja meg az [egyedi kulcsok](unique-keys.md) egy Azure Cosmos-tároló létrehozásakor. Ez a művelet végrehajtásához, vagy az Azure portal használatával, vagy az SDK-k egyikével jelenleg lehetőség.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account) , vagy válasszon ki egy meglévőt.

1. Nyissa meg a **adatkezelő** ablaktáblán, és válassza ki a tárolót, hogy a használni kívánt.

1. Kattintson a **új tároló**.

1. Az a **tároló hozzáadása** párbeszédpanel, kattintson a **+ egyedi kulcs hozzáadása** egyedi bejegyzés hozzáadása.

1. Adja meg az elérési úton a egyedi key megkötés

1. Szükség esetén adjon hozzá további egyedi kulcsbejegyzésekhez kattintva **+ egyedi kulcs hozzáadása**

![Egyedi kulcsmegkötés bejegyzés az Azure Portal képernyőképe](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>A .NET SDK V2 használata

Létrehozásakor egy új tárolót használ a [.NET SDK-val v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), amely egy `UniqueKeyPolicy` objektum egyedi kulcsra vonatkozó megkötések meghatározására használható.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-java-sdk"></a>Use the Java SDK

Létrehozásakor egy új tárolót használ a [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), amely egy `UniqueKeyPolicy` objektum egyedi kulcsra vonatkozó megkötések meghatározására használható.

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

## <a name="use-the-nodejs-sdk"></a>A Node.js SDK-val

Egy új tárolót a létrehozásakor a [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos), amely egy `UniqueKeyPolicy` objektum egyedi kulcsra vonatkozó megkötések meghatározására használható.

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

Egy új tárolót a létrehozásakor a [Python SDK](https://pypi.org/project/azure-cosmos/), egyedi kulcsokra vonatkozó korlátozások a szótárban, a paraméterként részeként adható meg.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            { 'paths': ['/firstName', '/lastName', '/emailAddress'] },
            { 'paths': ['/address/zipCode'] }
        ]
    }
})
```

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [particionálása](partition-data.md)
- Ismerkedés a [indexelési útmutató](index-overview.md)