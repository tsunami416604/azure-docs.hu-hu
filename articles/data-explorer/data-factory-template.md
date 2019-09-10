---
title: Azure Data Factory sablon használata az adatbázisból az Azure-ba való tömeges másoláshoz Adatkezelő
description: Ebben a témakörben megtudhatja, hogyan használhat Azure Data Factory sablont az adatbázisból az Azure-ba való tömeges másoláshoz Adatkezelő
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 5a6aebd276ef8658da9ca763be7da5c38a9c772a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873430"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>Azure Data Factory sablon használata az adatbázisból az Azure-ba való tömeges másoláshoz Adatkezelő

Az Azure Adatkezelő egy gyors, teljes körűen felügyelt adatelemzési szolgáltatás, amellyel a nagy mennyiségű adatfolyamot, többek között az alkalmazásokat, webhelyeket és IoT-eszközöket is valós időben elemezheti. A Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. Az Azure Data Factory szolgáltatással feltöltheti az Azure Adatkezelő-adatbázist a meglévő rendszerből származó adatokkal, és időt takaríthat meg az elemzési megoldások kiépítésekor. 

[Azure Data Factory sablonok](/azure/data-factory/solution-templates-introduction) előre definiált Azure Data Factory folyamatok, amelyek lehetővé teszik a Data Factory gyors megkezdését, és a fejlesztési idő csökkentését az adatintegrációs projektek létrehozásához. Az **adatbázisból az Azure** -ba adatkezelő sablonba történő tömeges másolás a **keresési** és a **foreach** tevékenységek használatával jön létre. A sablonnal több folyamat is létrehozható adatbázis vagy tábla használatával az adatok gyorsabb másolásához. 

> [!IMPORTANT]
> * A **tömeges másolás az adatbázisból az azure adatkezelő** sablonba nagy mennyiségű adatok másolásához az adatbázisokból, például az SQL Serverből és a Google BigQuery az Azure Adatkezelőba. 
> * A [Másolás eszköz](data-factory-load-data.md) használatával néhány táblázatot kis vagy közepes mennyiségű adattal másolhat az Azure Adatkezelőba. 

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Azure Adatkezelő-fürt és-adatbázis](create-cluster-database-portal.md)
* [Adatelőállító létrehozása](data-factory-load-data.md#create-a-data-factory)
* Adatbázisbeli adatforrások

## <a name="create-controltabledataset"></a>ControlTableDataset létrehozása

A **ControlTableDataset** azt jelzi, hogy milyen adatok lesznek átmásolva a forrás és a cél között a folyamatba. A sorok száma az adatmásoláshoz szükséges folyamatok teljes számát jelzi. A **ControlTableDataset** a forrás-adatbázis részeként kell meghatározni.

Példa SQL Server forrástábla formátumára:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```
    
|Tulajdonság  |Leírás  | Példa
|---------|---------| ---------|
|PartitionId   |   másolási sorrend | 1  |  
|SourceQuery   |   az a lekérdezés, amely azt jelzi, hogy mely adatcsatornák lesznek átmásolva a folyamat futási ideje alatt | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  cél tábla neve | MyAdxTable       |  

Ha a **ControlTableDataset** formátuma eltérő, hozzon létre egy hasonló **ControlTableDataset** a formátumhoz.

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>Tömeges másolás használata az adatbázisból az Azure Adatkezelő sablonba

1. Az **első lépések** lapon válassza a **folyamat létrehozása sablonból** csempét, vagy válassza a ceruza ikont (**Szerző** lap) a jobb oldali > **+**  >  **folyamat sablonból** a sablon-gyűjtemény megnyitásához.

    ![Azure Data Factory első lépések](media/data-factory-template/adf-get-started.png)

1. Válassza ki **a sablon tömeges másolását az adatbázisból az Azure Adatkezelőba**.
 
    ![Folyamat kiválasztása sablonból](media/data-factory-template/pipeline-from-template.png)

1.  A **tömeges másolás az adatbázisból az Azure** -ba adatkezelő ablakban válassza a meglévő adatkészletek lehetőséget a legördülő listából. 

    * **ControlTableDataset** -társított szolgáltatás a táblához, amely jelzi, hogy a forrásról a célhelyre másolt adatok és hová kerül a célhelyre. 
    * **SourceDataset** – társított szolgáltatás a forrás-adatbázishoz. 
    * **AzureDataExplorerTable** – Azure adatkezelő tábla. Ha az adatkészlet nem létezik, [hozza létre az Azure adatkezelő társított szolgáltatást](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) az adatkészlet hozzáadásához.
    * Válassza **a sablon használata**lehetőséget.

    ![Azure-Adatkezelő sablon tömeges másolásának konfigurálása](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. A sablon folyamatának eléréséhez válasszon ki egy, a tevékenységen kívüli területen a vásznon. Válassza a **Paraméterek** lehetőséget a tábla paramétereinek megadásához, beleértve a **nevet** (vezérlő tábla neve) és az **alapértelmezett értéket** (oszlopnevek).

    ![Folyamat paraméterei](media/data-factory-template/pipeline-parameters.png)

1.  Az alapértelmezett beállítások megtekintéséhez válassza a keresési tevékenység, **GetPartitionList**lehetőséget. A lekérdezés automatikusan létrejön.
1.  Válassza a parancs tevékenység **ForEachPartition**lehetőséget, majd válassza a **Beállítások** lehetőséget.
    * **Kötegek száma**: Válassza ki a számot a 1-50-ből. Ez a beállítás határozza meg a párhuzamosan futó folyamatok számát, amíg el nem éri a sorok **ControlTableDataset** számát. 
    * Ne jelölje be a **szekvenciális** jelölőnégyzetet, hogy a folyamat kötegei párhuzamosan fussanak.

    ![ForEachPartition-beállítások](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Az ajánlott eljárás az, hogy egyszerre több folyamatot futtasson párhuzamosan, így az adatmásolási folyamat gyorsabban másolható. A hatékonyság növelése érdekében particionálja a forrástábla adatait, és egy-egy partíciót oszt ki a dátum és a tábla szerint.

1. Válassza az **összes ellenőrzése** lehetőséget az ADF-folyamat ellenőrzéséhez. Lásd: **folyamat-ellenőrzési kimenet**.

    ![Sablon-folyamatok ellenőrzése](media/data-factory-template/validate-template-pipelines.png)

1. Ha szükséges, válassza a **hibakeresés**lehetőséget, majd **adja hozzá az triggert** a folyamat futtatásához.

    ![Folyamat futtatása](media/data-factory-template/trigger-run-of-pipeline.png)    


Mostantól a **tömeges másolást is használhatja az adatbázisból az Azure adatkezelő** sablonba, így az adatbázisok és táblák nagy mennyiségű adatainak hatékonyan másolhatók.

## <a name="next-steps"></a>További lépések

* Az [Azure Adatkezelőba való adatmásolási eljárás ismertetése Azure Data Factory használatával](data-factory-load-data.md).

* Ismerkedjen meg a Azure Data Factory [Azure adatkezelő-összekötővel](/azure/data-factory/connector-azure-data-explorer) .

* Ismerje meg az Azure Adatkezelő Adatlekérdezési [lekérdezéseit](/azure/data-explorer/web-query-data) .






