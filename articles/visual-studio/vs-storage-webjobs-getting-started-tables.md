---
title: Az Azure-tárhely használatának első lépései a Visual Studio használatával (WebJob-projektek)
description: Az Azure Table storage használatának első lépései egy Azure WebJobs-projektben a Visual Studióban, miután a Visual Studio csatlakoztatott szolgáltatásait használva egy tárfiókhoz csatlakoztak
services: storage
author: ghogen
manager: jillfra
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e4d8299c06bfa5b0f33bff8fa592a2fa549c695c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707604"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Az Azure Storage – első lépések (Azure WebJob-projektek)

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk C# kódmintákat tartalmaz, amelyek bemutatják, hogyan használhatja az Azure WebJobs SDK 1.x-es verzióját az Azure table storage szolgáltatással. A kódminták a [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 1.x verzióját használják.

Az Azure Table storage szolgáltatás lehetővé teszi, hogy nagy mennyiségű strukturált adatok tárolására. A szolgáltatás egy NoSQL-adattár, amely fogadja a hitelesített hívásokat az Azure-felhőn belülről és kívülről. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.  További információ: Az Azure Table storage használatával a [.NET használatával](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) című témakörben talál további információt.

A kódrészletek egy része a **manuálisan** megnevezett függvényekben használt Table attribútumot jeleníti meg, azaz nem az eseményindító attribútumok egyikének használatával.

## <a name="how-to-add-entities-to-a-table"></a>Entitások hozzáadása táblához

Entitások táblához való hozzáadásához használja a **Table** attribútumot egy **ICollector\<T>** vagy **IAsyncCollector\<T>** paraméterrel, ahol **T** megadja a hozzáadni kívánt entitások sémáját. Az attribútumkonstruktor olyan karakterlánc-paramétert vesz fel, amely megadja a tábla nevét.

A következő kódminta **személyentitásokat** ad hozzá a *Beress*nevű táblához.

```csharp
[NoAutomaticTrigger]
public static void IngressDemo(
    [Table("Ingress")] ICollector<Person> tableBinding)
{
    for (int i = 0; i < 100000; i++)
    {
        tableBinding.Add(
            new Person() {
                PartitionKey = "Test",
                RowKey = i.ToString(),
                Name = "Name" }
            );
    }
}
```

Az **ICollector-rel** használt típus általában a **TableEntity** alkalmazásból származik, vagy az **ITableEntity-t**implementálja, de nem kell. Az alábbi **személyosztályok** bármelyike az előző **bejövő** kódban látható kóddal működik.

```csharp
public class Person : TableEntity
{
    public string Name { get; set; }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

Ha közvetlenül az Azure storage API-val szeretne dolgozni, hozzáadhat egy **CloudStorageAccount** paramétert a metódus aláírásához.

## <a name="real-time-monitoring"></a>Valós idejű megfigyelés

Mivel az adatgyűjtő funkciók gyakran nagy mennyiségű adatot dolgoznak fel, a WebJobs SDK irányítópult valós idejű figyelési adatokat biztosít. A **Meghívási napló** szakasz jelzi, hogy a függvény még fut-e.

![A be- és visszafutás függvény](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

A **Meghívás részletei** lap jelenti a függvény előrehaladását (a megírt entitások számát), miközben fut, és lehetőséget ad a megszakítására.

![A be- és visszafutás függvény](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Amikor a függvény befejeződik, a **Meghívás részletei** lap a megírt sorok számát jelenti.

![A be- és visszaverésfunkció funkció befejeződött](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Több entitás beolvasása egy táblából

Táblázat olvasásához használja a **Table** attribútumot **egy\<IQueryable T>** paraméterrel, ahol a **T** típus **a TableEntity-ból** származik, vagy az **ITableEntity**eszközt implementálja.

A következő kódminta beolvassa és naplózza a **Be- és abelépési tábla** összes sorát:

```csharp
public static void ReadTable(
    [Table("Ingress")] IQueryable<Person> tableBinding,
    TextWriter logger)
{
    var query = from p in tableBinding select p;
    foreach (Person person in query)
    {
        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
            person.PartitionKey, person.RowKey, person.Name);
    }
}
```

### <a name="how-to-read-a-single-entity-from-a-table"></a>Egyetlen entitás olvasása táblázatból

Van egy **tábla** attribútumkonstruktor két további paraméterrel, amely lehetővé teszi a partíciókulcs és a sorkulcs megadását, ha egyetlen táblaentitáshoz szeretne kötni.

A következő kódminta egy **személy** entitás táblasorát olvassa be a válaszüzenetben kapott partíciókulcs és sorkulcs értékek alapján:

```csharp
public static void ReadTableEntity(
    [QueueTrigger("inputqueue")] Person personInQueue,
    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
    TextWriter logger)
{
    if (personInTable == null)
    {
        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                personInQueue.PartitionKey, personInQueue.RowKey);
    }
    else
    {
        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
    }
}
```

Ebben a példában a **Személy** osztálynak nem kell végrehajtania az **ITableEntity -ot.**

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>A .NET Storage API közvetlen használata egy táblával való munkához

A **Table** attribútumot **egy CloudTable** objektummal is használhatja, hogy rugalmasabban dolgozhasson egy táblával.

A következő kódminta egy **CloudTable** objektumot használ, hogy egyetlen entitást adjon hozzá a *be- és előtér-táblához.*

```csharp
public static void UseStorageAPI(
    [Table("Ingress")] CloudTable tableBinding,
    TextWriter logger)
{
    var person = new Person()
        {
            PartitionKey = "Test",
            RowKey = "100",
            Name = "Name"
        };
    TableOperation insertOperation = TableOperation.Insert(person);
    tableBinding.Execute(insertOperation);
}
```

A **CloudTable** objektum használatáról az Azure Table storage használatával [a .NET használatával](../storage/storage-dotnet-how-to-use-tables.md)című témakörben talál további információt.

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>A várólisták útmutatócikke által lefedett kapcsolódó témakörök

A várólistaüzenet által kiváltott táblafeldolgozás kezeléséről, illetve a nem a táblázatfeldolgozásra jellemző WebJobs SDK-forgatókönyvekről az [Azure Queue storage és a Visual Studio csatlakoztatott szolgáltatásainak (WebJob-projektek) című](../storage/vs-storage-webjobs-getting-started-queues.md)témakörben talál további információt.

## <a name="next-steps"></a>További lépések

Ez a cikk olyan kódmintákat tartalmaz, amelyek bemutatják, hogyan kezelhetők az Azure-táblákkal való együttműködés gyakori forgatókönyvei. Az Azure WebJobs és a WebJobs SDK használatáról az [Azure WebJobs dokumentációs erőforrásaiban](https://go.microsoft.com/fwlink/?linkid=390226)olvashat bővebben.
