---
title: Első lépések az Azure Storage és a Visual Studio csatlakoztatott szolgáltatásaival (Webjobs-projektek)
description: Az Azure Table Storage használatának első lépései egy Azure WebJobs-projektben a Visual Studióban, miután kapcsolódott egy Storage-fiókhoz a Visual Studio Connected Services használatával
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
ms.openlocfilehash: 8875f680c8bb83c2375d6fe767f376cbb35d5a0a
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510663"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Első lépések Azure Storage-ban (Azure Webjobs-projektek)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk C# a Azure WebJobs SDK 1. x verziójának az Azure Table Storage szolgáltatással való használatát bemutató példákat tartalmaz. A kód minták a [Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 1. x verzióját használják.

Az Azure Table Storage szolgáltatás lehetővé teszi nagy mennyiségű strukturált adattárolás tárolását. A szolgáltatás egy NoSQL-adattár, amely az Azure-felhőn belüli és kívüli hitelesített hívásokat fogadja el. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.  További információkért lásd: az [Azure Table Storage használatának első lépései a .NET használatával](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) .

A kódrészletek némelyike a manuálisan létrehozott függvények **táblázatban** szereplő attribútumot jeleníti meg, azaz nem az egyik trigger attribútum használatával.

## <a name="how-to-add-entities-to-a-table"></a>Entitások hozzáadása táblához
Ha entitásokat szeretne felvenni egy táblába, használja a **Table** attribútumot egy **ICollector\<t >** vagy **IAsyncCollector\<t >** paraméterrel, ahol a **t** meghatározza a felvenni kívánt entitások sémáját. Az attribútum konstruktora egy olyan karakterlánc-paramétert hoz létre, amely megadja a tábla nevét.

A következő mintakód hozzáadja a **személy** entitásokat egy *bejövő*elemek nevű táblához.

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

A **ICollector** -ben használt típus általában **TableEntity** -ből származik, vagy a **ITableEntity**implementálja, de nem szükséges. A következő osztályok egyike az előző bemenő eljárásban látható kóddal működik együtt.

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

Ha közvetlenül az Azure Storage API-val szeretne dolgozni, **CloudStorageAccount** paramétert adhat hozzá a metódus aláírásához.

## <a name="real-time-monitoring"></a>Valós idejű figyelés
Mivel a beáramlási funkciók gyakran nagy mennyiségű adat feldolgozását dolgozzák fel, a webjobs SDK-irányítópult valós idejű figyelési adatokkal szolgál. A **hívási napló** szakasz azt jelzi, hogy a függvény még fut-e.

![A bejövő forgalom funkció fut](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

A **Meghívási részletek** lap jelentést készít a függvény előrehaladásáról (az írt entitások száma), miközben futtatja, és lehetőséget ad a megszakításra.

![A bejövő forgalom funkció fut](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

A függvény befejeződése után a Meghívási **részletek** lap az írt sorok számát jelenti.

![A bejövő forgalom funkció befejeződött](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Több entitás beolvasása egy táblából
Egy tábla olvasásához használja a **Table** attribútumot egy **IQueryable\<T >** paraméterrel, ahol a **t** típus a **TableEntity** -ből származik, vagy **ITableEntity**valósít meg.

A következő mintakód beolvassa és naplózza az összes sort a **bejövő** forgalom táblából:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Egyetlen entitás beolvasása egy táblából
A **Table** Attribute konstruktor két további paraméterrel rendelkezik, amelyek lehetővé teszik a partíciós kulcs és a sor kulcs megadását, ha egyetlen tábla entitáshoz kíván kötni.

A következő mintakód beolvas egy **személy** entitáshoz tartozó táblázat sort egy üzenetsor-üzenetben kapott partíciós kulcs és sor kulcs értéke alapján:  

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


Ebben a példában a **person** osztálynak nem kell implementálnia a **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>A .NET Storage API közvetlen használata egy táblázattal való együttműködéshez
A **Table** attribútumot egy **CloudTable** objektummal is használhatja, hogy rugalmasabb legyen a tábla használata.

A következő mintakód egy **CloudTable** objektumot használ, amely egyetlen entitást ad hozzá a *bejövő* táblákhoz.

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

További információ a **CloudTable** objektum használatáról: az [Azure Table Storage használatának első lépései a .NET használatával](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>A várólisták útmutató cikke által tárgyalt kapcsolódó témakörök
További információ a várólista-üzenet által aktivált táblázatos feldolgozás kezeléséről, illetve a táblázatos feldolgozásra nem jellemző webjobs SDK-forgatókönyvekről: [Ismerkedés az Azure üzenetsor-tárolóval és a Visual Studio csatlakoztatott szolgáltatásaival (webjobs-projektek)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>További lépések
Ez a cikk az Azure Tables használatának gyakori forgatókönyveit bemutató példákat tartalmaz. További információ a Azure WebJobs és a webjobs SDK használatáról: [Azure WebJobs dokumentációs erőforrások](https://go.microsoft.com/fwlink/?linkid=390226).

