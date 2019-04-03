---
title: Ismerkedés az Azure storage és a Visual Studio csatlakoztatott szolgáltatásainak (webjobs-feladat projektek)
description: Ismerkedés az Azure Table storage használata az Azure WebJobs-projektben a Visual Studióban egy tárfiókot, a Visual Studio használatával való csatlakozást követően kapcsolódó szolgáltatások
services: storage
author: ghogen
manager: douge
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: a9a4475465fefb01ec53e6e0eb814f9b8f192a1b
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850207"
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Ismerkedés az Azure Storage (az Azure webjobs-feladat projektek)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk a C#-mintakódot biztosít, amelyek bemutatják az Azure WebJobs SDK-verzió használatához az Azure table storage szolgáltatással 1.x. A Kódminták használja a [WebJobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki) verzió 1.x.

Az Azure Table storage szolgáltatás lehetővé teszi nagy mennyiségű strukturált adat tárolására. A szolgáltatás egy NoSQL-adattár, amely elfogadja az érkező hitelesített hívásokat belül és kívül az Azure-felhőben. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak.  Lásd: [.NET használatával az Azure Table storage használatának első lépései](../cosmos-db/tutorial-develop-table-dotnet.md#create-a-table) további információt.

A kód a kódrészletek megjelenítése némelyike a **tábla** függvények, melynek neve manuálisan, vagyis nem használatával az eseményindító-attribútumok közül a használt attribútum.

## <a name="how-to-add-entities-to-a-table"></a>Entitások hozzáadása a táblához
Entitások hozzáadása táblázathoz, használja a **tábla** az attribútum egy **ICollector<T>**  vagy **IAsyncCollector<T>**  paraméter ahol **T** adja meg a hozzáadni kívánt entitásokat sémája. Az attribútum konstruktorának paramétereként egy karakterlánc-paramétert, amely meghatározza a tábla neve.

A következő példakód hozzáadja **személy** nevű tábla az entitások *bejövő*.

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

Általában a típust használja **ICollector** származó **TableEntity** vagy valósít meg **ITableEntity**, azonban nem kell. A következők egyikét **személy** látható az előző kód a munkahelyi osztályokat **bejövő** metódust.

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

Ha az ügyfelek közvetlenül az Azure storage API-t szeretne, hozzáadhat egy **CloudStorageAccount** paramétert a metódus aláírásához.

## <a name="real-time-monitoring"></a>Valós idejű figyelés
Bejövő forgalom adatfüggvény gyakran feldolgozásához nagy mennyiségű adatot, mert a WebJobs SDK-val irányítópult biztosít valós idejű monitorozási adatok. A **Hívásnapló** szakasz bemutatja, hogy a funkció továbbra is fut-e.

![Bejövő függvény](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

A **hívás részletei** lap (írt entitások száma). a függvény végrehajtási jelentések közben fut, és elveti azt lehetőséget biztosít.

![Bejövő függvény](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

A függvény befejeztével a **hívás részletei** lap írt sorok számát jelenti.

![Bejövő függvény befejeződött](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Egy tábla több entitás olvasása
Olvassa el a táblát, használja a **tábla** az attribútum egy **IQueryable<T>**  paraméter ahol **T** származó **TableEntity**vagy valósít meg **ITableEntity**.

A következő mintakód beolvassa és naplózza az összes sort a **bejövő** tábla:

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

### <a name="how-to-read-a-single-entity-from-a-table"></a>Egy tábla egyetlen entitás olvasása
Van egy **tábla** két további paraméterek, amelyek segítségével megadhatja a partíciókulccsal és sorkulccsal, ha egyetlen tábla entitáshoz kötni kívánt, hogy az attribútumok konstruktorában.

A következő mintakód beolvassa egy tábla sort egy **személy** entitás alapján partíciós kulcs és a sor kulcs értékeit az üzenetsori üzenet érkezett:  

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


A **személy** ebben a példában az osztály nem rendelkezik megvalósításához **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Közvetlenül a tábla használata a Storage .NET API használata
Is használhatja a **tábla** az attribútum egy **CloudTable** objektum a egy tábla használatát a nagyobb rugalmasság biztosítására.

A következő kódban minta egy **CloudTable** objektum az egyetlen entitás hozzáadása a *bejövő* tábla.

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

Használatáról további információt a **CloudTable** objektumazonosító, lásd: [.NET használatával az Azure Table storage használatának első lépései](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Az üzenetsorok útmutató cikkben foglalt Kapcsolódó témakörök
Egy üzenetsor által aktivált tábla feldolgozása kezelése kapcsolatos információkat, vagy a WebJobs SDK-val forgatókönyveket nem tábla feldolgozása, lásd: [Ismerkedés az Azure Queue storage és a Visual Studio csatlakoztatott szolgáltatásainak (webjobs-feladat projektek) ](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>További lépések
Ebben a cikkben nyújtott kódmintákkal, amelyek bemutatják, hogyan kezelje az Azure-táblák használata gyakori forgatókönyvei. Azure WebJobs és WebJobs SDK használatával kapcsolatos további információkért lásd: [Azure WebJobs-dokumentáció erőforrások](https://go.microsoft.com/fwlink/?linkid=390226).

