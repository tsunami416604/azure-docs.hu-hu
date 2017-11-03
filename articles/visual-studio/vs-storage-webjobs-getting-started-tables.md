---
title: "Ismerkedés az Azure storage és a Visual Studio csatlakoztatva (webjobs-feladat projektek) szolgáltatások"
description: "Ismerkedés az Azure Table storage használata az Azure webjobs-feladatok projekt, a Visual Studio egy tárfiókot, a Visual Studio használatával történő kapcsolódás után kapcsolódó szolgáltatások"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 4e0c77e08bff971277a09d6066f259db84617616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Ismerkedés az Azure Storage (az Azure webjobs-feladat projektek)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a C# mintakódok megjelenítése használatát mutatják be az Azure WebJobs SDK-verzió 1.x az Azure table storage szolgáltatással. A kód minták használja a [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verzió 1.x.

Az Azure Table storage szolgáltatás lehetővé teszi nagy mennyiségű strukturált adatok tárolásához. A szolgáltatás egy NoSQL-adattár, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.  Lásd: [Ismerkedés az Azure Table storage használatának .NET](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table) további információt.

A kód részletek megjelenítése egy részénél a **tábla** attribútum függvényekben, melynek neve manuálisan, ez azt jelenti, hogy nem használatával az eseményindító attribútumok egyikét használja.

## <a name="how-to-add-entities-to-a-table"></a>Egy tábla entitások felvétele
Egy táblához entitások hozzáadásához használja a **tábla** az attribútum egy **ICollector<T>**  vagy **IAsyncCollector<T>**  paraméter ahol **T** határozza meg a hozzáadni kívánt entitásokat sémája. Az attribútum konstruktora tart egy karakterlánc-paramétert, amely megadja a tábla neve.

A következő példakód hozzáadja **személy** nevű tábla az entitások *érkező*.

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

Általában a típust használ **ICollector** származik **TableEntity** vagy megvalósítja **ITableEntity**, de nem kell. Az alábbiak valamelyikét **személy** munkahelyi osztályok az előző látható kóddal **érkező** metódust.

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

Ha közvetlenül az Azure storage API dolgozni szeretne, hozzáadhat egy **CloudStorageAccount** a metódus-aláírás paramétert.

## <a name="real-time-monitoring"></a>Valós idejű figyelése
Adatbeviteli függvényektől a nagy mennyiségű adatot gyakran feldolgozni, mert a WebJobs SDK irányítópult biztosít a valós idejű figyelési adatok. A **meghívása napló** szakasz azt ismerteti, hogy a funkció továbbra is fut-e.

![Érkező függvény fut](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

A **meghívása részletek** lap jelent a függvény folyamatban (írt entitások száma) működik, valamint lehetővé teszi az megszakítható.

![Érkező függvény fut](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

A függvény befejezése után a **meghívása részletek** lap jelentések írása sorok száma.

![Befejezett érkező függvény](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Egy táblázat több entitás beolvasása
Egy tábla olvasni, használja a **tábla** az attribútum egy **IQueryable<T>**  paraméter ahol írja be a **T** származik **TableEntity** vagy megvalósítja **ITableEntity**.

A következő példakód olvas, és naplózza az összes sorát a **érkező** tábla:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>A táblázat egyetlen entitás beolvasása
Van egy **tábla** két olyan további paraméterekkel, amelyek lehetővé teszik a partíciós kulcs és a sorkulcs megadásakor egyetlen táblaentitássá kötni kívánt attribútumok konstruktorában.

Az alábbi példakód egy tábla sort beolvassa a **személy** entitás partíció kulcs és a sor kulcsértékei egy üzenetsor-üzenetet kapott alapján:  

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


A **személy** ebben a példában az osztály nem valósítja meg kell **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Hogyan használhatja a .NET tárolási API közvetlenül a táblázat
Használhatja a **tábla** az attribútum egy **CloudTable** objektum végzett munka során egy táblázat további rugalmasságot biztosít.

A következő kódban a minta egy **CloudTable** objektum hozzáadása az egyetlen entitás az *érkező* tábla.

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

Használatáról további információt a **CloudTable** objektumazonosító, lásd: [Ismerkedés az Azure Table storage használatának .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Az útmutató a várólisták a cikkben említett kapcsolódó témakörök
Információ a tábla feldolgozási sor üzenetet által indított kezelésének módját, vagy nem kizárólag a tábla feldolgozásra WebJobs SDK-forgatókönyvek esetén lásd: [Ismerkedés az Azure Queue storage és a Visual Studio csatlakoztatva (webjobs-feladat projektek) szolgáltatások](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Következő lépések
Ez a cikk nyújtott mintakódok, amelyek bemutatják, hogyan kezeli az Azure-táblákban használata gyakori forgatókönyvei. Azure webjobs-feladatok és a WebJobs SDK használatával kapcsolatos további információkért lásd: [Azure WebJobs-dokumentáció erőforrások](http://go.microsoft.com/fwlink/?linkid=390226).

