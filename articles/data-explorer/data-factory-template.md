---
title: Másolás tömegesen egy adatbázisból az Azure Data Explorerbe az Azure Data Factory sablon használatával
description: Ebben a cikkben megtudhatja, hogy egy Azure Data Factory sablon használatával másolhat tömegesen egy adatbázisból az Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293555"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Másolás tömegesen egy adatbázisból az Azure Data Explorerbe az Azure Data Factory sablon használatával 

Az Azure Data Explorer egy gyors, teljes körűen felügyelt adatelemzési szolgáltatás. Valós idejű elemzést kínál nagy mennyiségű adatról, amelyek számos forrásból, például alkalmazásokból, webhelyekről és IoT-eszközökből származnak. 

Ha adatokat szeretne másolni egy adatbázisból az Oracle Server, a Netezza, a Teradata vagy az SQL Server szolgáltatásból az Azure Data Explorerbe, hatalmas mennyiségű adatot kell betöltenie több táblából. Az adatokat általában minden táblában particionálni kell, hogy egyetlen táblából párhuzamosan több szálat tartalmazó sorokat tölthessen be. Ez a cikk az ilyen esetekben használható sablont ismerteti.

[Az Azure Data Factory-sablonok](/azure/data-factory/solution-templates-introduction) előre definiált Data Factory-folyamatok. Ezek a sablonok segítségével gyorsan elkezdheti a Data Factory programot, és csökkentheti az adatintegrációs projektek fejlesztési idejét. 

Hozzon létre a *tömeges másolás adatbázisból az Azure Data Explorer* sablon segítségével *Lookup* és *ForEach* tevékenységek használatával. A gyorsabb adatmásolás érdekében a sablon segítségével adatbázisonként vagy táblánként több folyamatot hozhat létre. 

> [!IMPORTANT]
> Ügyeljen arra, hogy a másolni kívánt adatmennyiségnek megfelelő eszközt használja.
> * Az *adatbázisból* az Azure Data Explorer sablonba nagy mennyiségű adatot másolhat adatbázisokból, például az SQL-kiszolgálóról és a Google BigQueryből az Azure Data Explorerbe. 
> * A [*Data Factory Copy Data eszközzel*](data-factory-load-data.md) néhány kis vagy közepes mennyiségű adatot feldolgozó táblát másolhat az Azure Data Explorerbe. 

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Egy Azure Data Explorer-fürt és -adatbázis.](create-cluster-database-portal.md)
* [Hozzon létre egy adat-előállító](data-factory-load-data.md#create-a-data-factory).
* Az adatbázis ban lévő adatforrás.

## <a name="create-controltabledataset"></a>ControlTableDataset létrehozása

*A ControlTableDataset* azt jelzi, hogy milyen adatok lesznek másolva a forrásból a folyamatban lévő célhoz. A sorok száma az adatok másolásához szükséges folyamatok teljes számát jelzi. A ControlTableDataset-et a forrásadatbázis részeként kell definiálni.

Az SQL Server forrástábla formátumának egy példája a következő kódban látható:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

A kódelemeket a következő táblázat ismerteti:

|Tulajdonság  |Leírás  | Példa
|---------|---------| ---------|
|PartitionId   |  A másolási sorrend | 1  |  
|Forráslekérdezés   |  A lekérdezés, amely azt jelzi, hogy mely adatokat másolja a rendszer a folyamat futásideje alatt | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  A céltábla neve | MyAdxTábla       |  

Ha a ControlTableDataset más formátumban, hozzon létre egy hasonló ControlTableDataset az Ön formátumban.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>A tömeges másolás az adatbázisból az Azure Data Explorer sablonba

1. Az **Első lépések** ablaktáblán válassza a **Folyamat létrehozása sablonból lehetőséget** a **Sablongyűjtemény ablaktábla** megnyitásához.

    ![Az Azure Data Factory "Let's get get started" ablaktábla](media/data-factory-template/adf-get-started.png)

1. Válassza ki a **tömeges másolás adatbázisból az Azure Data Explorer** sablont.
 
    ![A "Tömeges másolás az adatbázisból az Azure Data Explorerbe" sablon](media/data-factory-template/pipeline-from-template.png)

1.  A **Tömeges másolás az adatbázisból** az Azure Data Explorer ablaktáblán a **Felhasználói bemenetek**csoportban adja meg az adatkészleteket az alábbi módon: 

    a. A **ControlTableDataset** legördülő listában válassza ki a vezérlőtáblához csatolt szolgáltatást, amely jelzi, hogy milyen adatokat másol a rendszer a forrásból a célhelyre, és hová kerül a célhelyre. 

    b. A **SourceDataset** legördülő listában jelölje ki a forrásadatbázishoz csatolt szolgáltatást. 

    c. Az **AzureDataExplorerTable** legördülő listában válassza ki az Azure Data Explorer-táblát. Ha az adatkészlet nem létezik, [hozza létre az Azure Data Explorer csatolt szolgáltatás](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) az adatkészlet hozzáadásához.

    d. Válassza **a Sablon használata lehetőséget.**

    ![A "Tömeges másolás az adatbázisból az Azure Data Explorerbe" ablaktábla](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. A sablonfolyamat eléréséhez jelöljön ki egy területet a vásznon a tevékenységeken kívül. A **Tábla** paramétereinek megadásához kattintson a Paraméterek fülre, beleértve a **Név** (vezérlőtábla neve) és **az Alapértelmezett értéket** (oszlopnevek).

    ![Folyamat paraméterei](media/data-factory-template/pipeline-parameters.png)

1.  A **Keres csoportban**válassza a **GetPartitionList** lehetőséget az alapértelmezett beállítások megtekintéséhez. A lekérdezés automatikusan létrejön.
1.  Válassza a Parancstevékenység **(ForEachPartition**) lehetőséget, válassza a **Beállítások** lapot, majd tegye a következőket:

    a. A **Kötegszám** mezőbe írjon be egy 1 és 50 között lévő számot. Ez a beállítás határozza meg a párhuzamosan futó folyamatok számát a *ControlTableDataset* sorok számának eléréséig. 

    b. Annak érdekében, hogy a folyamatkötegek párhuzamosan fussanak, *ne* jelölje be a **Szekvenciális jelölőnégyzetet.**

    ![ForEachPartition beállítások](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Az ajánlott eljárás az, hogy számos folyamatot párhuzamosan futtat, hogy az adatok gyorsabban másolhatók legyenek. A hatékonyság növelése érdekében particionálja az adatokat a forrástáblában, és lefoglalja egy partíciót folyamatonként, dátum és tábla szerint.

1. Válassza **az Összes ellenőrzése** az Azure Data Factory-folyamat érvényesítéséhez, majd tekintse meg az eredményt a **folyamatérvényesítési kimenet** ablaktáblában.

    ![Sablonfolyamatok ellenőrzése](media/data-factory-template/validate-template-pipelines.png)

1. Ha szükséges, válassza **a Hibakeresés**lehetőséget, majd a folyamat futtatásához válassza az **Eseményindító hozzáadása lehetőséget.**

    ![A "Debug" és a "Run pipeline" gombok](media/data-factory-template/trigger-run-of-pipeline.png)    

A sablon segítségével most már hatékonyan másolhat nagy mennyiségű adatot az adatbázisokból és táblákból.

## <a name="next-steps"></a>További lépések

* Megtudhatja, [hogyan másolhat adatokat az Azure Data Explorerbe az Azure Data Factory használatával.](data-factory-load-data.md)
* Ismerje meg az [Azure Data Explorer összekötő](/azure/data-factory/connector-azure-data-explorer) az Azure Data Factory.
* Ismerje meg az [Azure Data Explorer adatlekérdezési lekérdezések.](/azure/data-explorer/web-query-data)






