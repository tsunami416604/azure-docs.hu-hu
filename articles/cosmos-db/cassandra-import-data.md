---
title: Cassandra-adatok importálása az Azure Cosmos DB-be | Microsoft Docs
description: Ismerje meg, hogyan importálhat Cassandra-adatokat az Azure Cosmos DB-be a CQL másolás paranccsal.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 55a6fec1b6ac018b4b24c0d27dcfdd5812455800
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189676"
---
# <a name="migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Adatok migrálása az Azure Cosmos DB Cassandra API-fiókba

Ez az oktatóanyag bemutatja, hogyan importálhat Cassandra-adatokat az Azure Cosmos DB-be a Cassandra Query Language (CQL) COPY parancsával. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A kapcsolati sztring lekérése
> * Adatok importálása a CQLSH COPY parancsával
> * Importálás a Spark-összekötővel 

# <a name="prerequisites"></a>Előfeltételek

* Telepítse az [Apache Cassandra](http://cassandra.apache.org/download/) szolgáltatást, és győződjön meg arról, hogy a *cqlsh* elérhető.  

* Átviteli sebesség növelése: az adatok migrálásának időtartama a táblákhoz kiépített átviteli sebességtől függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. További információ az átviteli sebesség [Azure Portalon](https://portal.azure.com) való növeléséről: [Átviteli sebesség beállítása Azure Cosmos DB-tárolókhoz](set-throughput.md).  

* Az SSL engedélyezése: az Azure Cosmos DB szigorú biztonsági feltételekkel és szabványokkal rendelkezik. A fiókja használatakor mindig engedélyezze az SSL-t. Ha az SSH-val használja a CQL-t, akkor megadhat SSL-információkat. 

## <a name="get-your-connection-string"></a>A kapcsolati sztring beszerzése

1. Az [Azure Portalon](https://portal.azure.com) a bal szélen válassza az **Azure Cosmos DB** lehetőséget.

2. Az **Előfizetések** panelen válassza ki a fióknevét.

3. Kattintson a **Kapcsolati sztring** elemre. A jobb oldali panel tartalmazza a fiókhoz való kapcsolódáshoz szükséges összes információt.

    ![Kapcsolati sztring lap](./media/cassandra-import-data/keys.png)

## <a name="migrate-data-by-using-cqlsh-copy"></a>Adatok migrálása a cqlsh COPY használatával

Ha Cassandra-adatokat szeretne importálni az Azure Cosmos DB-be a Cassandra API-val való használathoz, használja az alábbi útmutatót:

1. Jelentkezzen be a cqhsh-ba a portál kapcsolati információival.
2. Használja a [CQL COPY parancsát](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) a helyi adatok Apache Cassandra API-végpontra való másolásához. A késés minimalizálása érdekében győződjön meg arról, hogy a forrás és a cél ugyanabban az adatközpontban található.

### <a name="steps-to-move-data-with-cqlsh"></a>Adatok áthelyezésének lépései a cqlsh használatával

1. Táblák előzetes létrehozása és skálázása:
    * Az Azure Cosmos DB alapértelmezés szerint másodpercenként 1000 kérelemegységgel (RU/s) hoz létre egy új Cassandra API-táblát (a CQL-alapú létrehozás másodpercenként 400 kérelemegységgel történik). A cqlsh-val végzett migrálás indítása előtt hozza létre előre az összes táblát az [Azure Portalról](https://portal.azure.com) vagy a cqlsh-ból. 

    * Az [Azure Portalon](https://portal.azure.com) növelje a táblák átviteli sebességét a migrálás időtartamára az alapértelmezett átviteli sebességről (400 vagy 1000 RU/s) 10000 RU/s-re. Nagyobb átviteli sebesség beállításakor nincs szükség a sebesség korlátozására, és gyorsabban végezhet a migrálással. Mivel az Azure Cosmos DB-ben órás egységekben történik a számlázás, költségtakarékossági okokból a migrálás után érdemes azonnal csökkentenie az átviteli sebességet.

2. Állapítsa meg a művelethez tartozó kérelemegységek díját. Ezt tetszőleges Azure Cosmos DB Cassandra API SDK-val megteheti. Ez a példa a kérelemegység-díjak .NET-tel való beszerzését mutatja be. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Állapítsa meg a gépe és az Azure Cosmos DB szolgáltatás közötti késést. Ha Azure-adatközpontban van, a késésnek egy kicsi, egyjegyű, ezredmásodpercet jelentő számnak kell lennie. Ha az Azure-adatközponton kívül van, használja a pspinget vagy az azurespeed.com-ot az adatközpont és a tartózkodási helye közötti hozzávetőleges késés megállapításához.   

4. Számítsa ki a paraméterek (NUMPROCESS, INGESTRATE, MAXBATCHSIZE, vagy MINBATCHSIZE) megfelelő értékeit, amelyek jó teljesítményt biztosítanak. 

5. Futtassa a végső migrálási parancsot. A parancs futtatása azt feltételezi, hogy elindította a cqlsh szolgáltatást a kapcsolati sztring információival.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-by-using-spark"></a>Adatok migrálása a Spark használatával

A meglévő fürtökben, Azure-beli virtuális gépeken található adatok esetében a Sparkkal való importálás is reális lehetőség. Ehhez a Sparkot közvetítőként kell beállítani egyszeri vagy rendszeres betöltéshez. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi feladatokat:

> [!div class="checklist"]
> * A kapcsolati sztring lekérése
> * Adatok importálása a cql copy paranccsal
> * Importálás a Spark-összekötővel 

Az Azure Cosmos DB-vel kapcsolatos további információért folytassa az Alapfogalmak szakasz áttekintésével. 

> [!div class="nextstepaction"]
>[Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](../cosmos-db/consistency-levels.md)
