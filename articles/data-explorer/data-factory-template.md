---
title: Adatok tömeges másolása egy adatbázisból az Azure Adatkezelőba a Azure Data Factory sablon használatával
description: Ebből a cikkből megtudhatja, hogyan használhat egy Azure Data Factory sablont egy adatbázisból az Azure-ba való tömeges másoláshoz Adatkezelő
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: ca50a1ecd4d2a21593ddd11f83337ae7476cf916
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300443"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Adatok tömeges másolása egy adatbázisból az Azure Adatkezelőba a Azure Data Factory sablon használatával 

Az Azure Adatkezelő egy gyors, teljes körűen felügyelt, adatelemzési szolgáltatás. Valós idejű elemzést biztosít nagy mennyiségű, több forrásból, például alkalmazásokból, webhelyekről és IoT származó adatokból. 

A Azure Data Factory egy teljes körűen felügyelt, felhőalapú, adatintegrációs szolgáltatás. A segítségével feltöltheti az Azure Adatkezelő-adatbázist a meglévő rendszerből származó adatokkal. És hasznos időt takaríthat meg az elemzési megoldások létrehozásakor. 

[Azure Data Factory sablonok](/azure/data-factory/solution-templates-introduction) előre definiált Data Factory folyamatok. Ezek a sablonok segíthetnek a Data Factory gyors megkezdésében, és a fejlesztési idő csökkentésében az adatintegrációs projektekben. 

A *tömeges másolást az adatbázisból az Azure adatkezelő* sablonba a *keresési* és a *foreach* tevékenységek segítségével hozhatja létre. A gyorsabb adatmásoláshoz a sablonnal több folyamat is létrehozható egy adatbázis vagy egy tábla alapján. 

> [!IMPORTANT]
> Ügyeljen arra, hogy a másolni kívánt adatmennyiséghez megfelelő eszközt használja.
> * A *tömeges másolás az adatbázisból az azure adatkezelő* sablonba nagy mennyiségű adatok másolásához az adatbázisokból, például az SQL Serverből és a Google BigQuery az Azure Adatkezelőba. 
> * A [*Data Factory adatok másolása eszköz*](data-factory-load-data.md) használatával néhány táblázatot kis vagy közepes mennyiségű adatból másolhat az Azure Adatkezelőba. 

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Azure adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md).
* [Hozzon létre egy adatelőállítót](data-factory-load-data.md#create-a-data-factory).
* Adatbázis adatforrása.

## <a name="create-controltabledataset"></a>ControlTableDataset létrehozása

A *ControlTableDataset* azt jelzi, hogy milyen adatok lesznek átmásolva a forrásról a folyamat célhelyére. A sorok száma az adatmásoláshoz szükséges folyamatok teljes számát jelzi. A ControlTableDataset a forrásadatbázis részeként kell meghatároznia.

A következő kódban látható egy példa a SQL Server forrástábla formátumára:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

A kód elemeit a következő táblázat ismerteti:

|Tulajdonság  |Leírás  | Példa
|---------|---------| ---------|
|PartitionId   |  A másolási sorrend | 1  |  
|SourceQuery   |  Az a lekérdezés, amely azt jelzi, hogy mely adatcsatornák lesznek átmásolva a folyamat futási ideje alatt | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  A céltábla neve | MyAdxTable       |  

Ha a ControlTableDataset formátuma eltérő, hozzon létre egy hasonló ControlTableDataset a formátumhoz.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Az adatbázisból származó tömeges másolás használata az Azure Adatkezelő sablonba

1. Az **első lépések** ablaktáblán válassza a **folyamat létrehozása sablonból** lehetőséget a **sablon-gyűjtemény** panel megnyitásához.

    ![Az "első lépések" panel Azure Data Factory](media/data-factory-template/adf-get-started.png)

1. Válassza ki az **adatbázisból az Azure adatkezelő sablonba való tömeges másolást** .
 
    !["Tömeges másolás az adatbázisból az Azure Adatkezelőba" sablon](media/data-factory-template/pipeline-from-template.png)

1.  A **tömeges másolás az adatbázisból az Azure** -ba adatkezelő ablaktáblán a **felhasználói bevitelek**területen adja meg az adatkészleteket a következő módon: 

    a. A **ControlTableDataset** legördülő listában válassza ki a társított szolgáltatást a vezérlő táblába, amely azt jelzi, hogy milyen adatok kerülnek a forrásból a célhelyre, és hogy hol kerül a célhelyre. 

    b. A **SourceDataset** legördülő listában válassza ki a társított szolgáltatást a forrás-adatbázishoz. 

    c. A **AzureDataExplorerTable** legördülő listában válassza ki az Azure adatkezelő táblázatot. Ha az adatkészlet nem létezik, [hozza létre az Azure adatkezelő társított szolgáltatást](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) az adatkészlet hozzáadásához.

    d. Válassza **a sablon használata**lehetőséget.

    ![A "tömeges másolás az adatbázisból az Azure Adatkezelőba" panel](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. A sablon folyamatának eléréséhez válasszon ki egy, a tevékenységen kívüli területen a vásznon. Válassza a **Parameters (paraméterek** ) fület a tábla paramétereinek megadásához, beleértve a **nevet** (vezérlő tábla neve) és az **alapértelmezett értéket** (oszlopnevek).

    ![Folyamat paraméterei](media/data-factory-template/pipeline-parameters.png)

1.  A **Keresés**területen válassza a **GetPartitionList** lehetőséget az alapértelmezett beállítások megtekintéséhez. A lekérdezés automatikusan létrejön.
1.  Válassza ki a parancs tevékenységét, **ForEachPartition**, válassza a **Beállítások** lapot, majd tegye a következőket:

    a. A **kötegek száma** mezőbe írjon be egy 1 és 50 közötti számot. Ez a beállítás határozza meg a párhuzamosan futó folyamatok számát, amíg el nem éri a *ControlTableDataset* -sorok számát. 

    b. Ha biztosítani szeretné, hogy a folyamat kötegei párhuzamosan *fussanak, ne* jelölje be a **szekvenciális** jelölőnégyzetet.

    ![ForEachPartition-beállítások](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Az ajánlott eljárás az, hogy egyszerre több folyamatot futtasson, így az adatai gyorsabban másolhatók. A hatékonyság növelése érdekében particionálja a forrás táblában lévő adatok mennyiségét, és a dátum és a tábla szerint egy partíciót foglaljon le folyamaton belül.

1. Jelölje be az **összes érvényesítése** jelölőnégyzetet a Azure Data Factory folyamat ellenőrzéséhez, majd tekintse meg az eredményt a **folyamat ellenőrzése kimenet** ablaktáblán.

    ![Sablon-folyamatok ellenőrzése](media/data-factory-template/validate-template-pipelines.png)

1. Ha szükséges, válassza a **hibakeresés**lehetőséget, majd válassza az **trigger hozzáadása** lehetőséget a folyamat futtatásához.

    ![A "hibakeresés" és a "folyamat futtatása" gomb](media/data-factory-template/trigger-run-of-pipeline.png)    

Mostantól a sablon használatával hatékonyan másolhat nagy mennyiségű adatmennyiséget az adatbázisaiból és a tábláiból.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [másolhat az Azure Adatkezelőba Azure Data Factory használatával](data-factory-load-data.md).
* Ismerkedjen meg a Azure Data Factory [Azure adatkezelő-összekötővel](/azure/data-factory/connector-azure-data-explorer) .
* Ismerje meg az Azure Adatkezelő Adatlekérdezési [lekérdezéseit](/azure/data-explorer/web-query-data) .






