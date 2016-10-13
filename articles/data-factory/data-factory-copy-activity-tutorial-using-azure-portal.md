<properties 
    pageTitle="Oktatóanyag: Másolási tevékenységet tartalmazó folyamat létrehozása az Azure Portallal | Microsoft Azure" 
    description="Ez az oktatóanyag bemutatja, hogyan hozhat létre Másolási tevékenységgel rendelkező Azure Data Factory-folyamatot az Azure Portal Data Factory Editor eszközével." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="09/16/2016" 
    ms.author="spelluru"/>


# Oktatóanyag: Másolási tevékenységet tartalmazó folyamat létrehozása az Azure Portallal
> [AZURE.SELECTOR]
- [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
- [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)



Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és figyelhet meg Azure data factoryt az Azure Portallal. Az adat-előállítóban szereplő adatcsatorna másolási tevékenységgel másolja az adatokat az Azure Blob Storage-ből az Azure SQL Database-be.

Az oktatóanyag során a következő lépéseket fogja elvégezni:

Lépés | Leírás
-----| -----------
[Azure data factory létrehozása](#create-data-factory) | Ebben a lépésben egy Azure data factoryt hoz létre **ADFTutorialDataFactory** néven.  
[Társított szolgáltatások létrehozása](#create-linked-services) | Ebben a lépésben létrehozza a következő két társított szolgáltatást: **AzureStorageLinkedService** és **AzureSqlLinkedService**. <br/><br/>Az AzureStorageLinkedService az Azure-tárolót, az AzureSqlLinkedService pedig az Azure SQL Database-t társítja az ADFTutorialDataFactory data factoryhez. A folyamat bemeneti adatai az Azure Blob Storage egyik blobtárolójában, a kimeneti adatok pedig az Azure SQL Database egyik táblájában találhatók. Ezért ezt a két adattárat társított szolgáltatásként kell hozzáadnia a data factoryhez.      
[Bemeneti és kimeneti adatkészletek létrehozása](#create-datasets) | Az előző lépésben társított szolgáltatásokat hozott létre, amelyek a bemeneti/kimeneti adatokat tartalmazó adattárakra hivatkoznak. Ebben a lépésben két adatkészletet határoz meg – az **InputDataset** és az **OutputDataset** adatkészletet –, amelyek az adattárakban tárolt bemeneti és kimeneti adatokat jelölik. <br/><br/>Az InputDataset adatkészlethez megadja a forrásadatokkal rendelkező blobot tároló blobtárolót, az OutputDataset adatkészlethez pedig megadja a kimeneti adatokat tároló SQL-táblát. Egyéb tulajdonságokat is megad, például a szerkezetet, rendelkezésre állást és a szabályzatot. 
[Folyamat létrehozása](#create-pipeline) | Ebben a lépésben létrehozza az **ADFTutorialPipeline** nevű folyamatot az ADFTutorialDataFactory elemben. <br/><br/>A folyamathoz hozzáad egy **Másolási tevékenységet**, amely bemeneti adatokat másol az Azure-blobból a kimeneti Azure SQL-táblába. A másolási tevékenység végzi az adattovábbítást az Azure Data Factoryban. Egy olyan, globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és méretezhető módon másolja át az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket. 
[Folyamat figyelése](#monitor-pipeline) | Ebben a lépésben a bemeneti és kimeneti táblák szeleteit figyeli az Azure Portal használatával.

## Előfeltételek 
Az oktatóanyag elvégzése előtt hajtsa végre [Az oktatóanyag áttekintése](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikkben előfeltételként felsorolt lépéseket.

## Data factory létrehozása
Ebben a lépésben az Azure Portal használatával létrehozza az **ADFTutorialDataFactory** nevű Azure data factoryt.

1.  Miután bejelentkezett az [Azure Portalra](https://portal.azure.com/), kattintson az **Új** elemre, és válassza az **Intelligencia és elemzés** lehetőséget, majd kattintson a **Data Factory** elemre. 

    ![New (Új)->DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)  

6. A **New data factory** (Új data factory) panelen:
    1. A**Name** (Név) mezőbe írja be a következőt: **ADFTutorialDataFactory**. 
    
        ![A New data factory (Új data factory) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)

        Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a data factory nevét (például sajátnévADFTutorialDataFactory-ra), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
    
            Data factory name “ADFTutorialDataFactory” is not available  
     
        ![A Data Factory name not available (A data factory neve nem érhető el) üzenet](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
    2. Jelölje ki az Azure-**előfizetést**.
    3. Az erőforráscsoportban tegye a következők egyikét:
        1. Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
        2. Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
    
            Az oktatóanyag egyes lépései azt feltételezik, hogy az **ADFTutorialResourceGroup** nevet adta az erőforráscsoportnak. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    4. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory szolgáltatás által támogatott régiók jelennek meg.
    5. Válassza **A kezdőpulton rögzít** lehetőséget.     
    6. Kattintson a **Létrehozás** gombra.

        > [AZURE.IMPORTANT] Data Factory-példány létrehozásához a [Data Factory közreműködője](../active-directory/role-based-access-built-in-roles.md/#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.
        >  
        >  A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.              
9.  Az állapotüzenetek/értesítési üzenetek megtekintéséhez kattintson az eszköztáron található csengő ikonra. 

    ![Értesítési üzenetek](./media/data-factory-copy-activity-tutorial-using-azure-portal/Notifications.png) 
10. A létrehozás befejezése után a **Data Factory** panel a képen látható módon jelenik meg.

    ![Data factory kezdőlap](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## Társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. A másolási tevékenység által támogatott forrásokért és fogadókért tekintse meg a [támogatott adattárak](data-factory-data-movement-activities.md##supported-data-stores-and-formats) című részt. A Data Factory által támogatott számítási szolgáltatások listájáért tekintse meg a [számítási társított szolgáltatások](data-factory-compute-linked-services.md) című részt. Az oktatóanyag során ne használjon számítási szolgáltatásokat. 

Ebben a lépésben létrehozza a következő két társított szolgáltatást: **AzureStorageLinkedService** és **AzureSqlLinkedService**. Az AzureStorageLinkedService társított szolgáltatás egy Azure Storage-fiókot, az AzureSqlLinkedService pedig egy Azure SQL Database-adatbázist társít az **ADFTutorialDataFactory** data factoryhez. Az oktatóanyag későbbi részében létrehoz egy folyamatot, amely adatokat másol az AzureStorageLinkedService blobtárolójából az AzureSqlLinkedService SQL-táblájába.

### Társított szolgáltatás létrehozása az Azure Storage-fiókhoz
1.  A **Data Factory** panelen kattintson az **Fejlesztés és üzembe helyezés** csempére a Data Factory **szerkesztőjének** elindításához.

    ![Az Author and deploy (Fejlesztés és üzembe helyezés) csempe](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
5. A **szerkesztőben** kattintson az eszköztár **New data store** (Új adattár) gombjára, és a legördülő menüben válassza az **Azure Storage** lehetőséget. A jobb oldali panelen megjelenik az Azure Storage társított szolgáltatás létrehozására szolgáló JSON-sablon. 

    ![A szerkesztő New data store (Új adattár) gombja](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
6. Az `<accountname>` és az `<accountkey>` kifejezéseket cserélje az Azure-tárfiókja nevére, illetve kulcsára. 

    ![Szerkesztő – A blobtároló JSON-fájlja](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png) 
6. Kattintson az eszköztár **Üzembe helyezés** gombjára. Az üzembe helyezett **AzureStorageLinkedService** szolgáltatásnak meg kell jelennie a fanézetben. 

    ![Szerkesztő – A blobtároló üzembe helyezése](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

> [AZURE.NOTE]
> A JSON-tulajdonságokkal kapcsolatos információk: [Adatok áthelyezése Azure Blobból vagy Azure Blobba](data-factory-azure-blob-connector.md#azure-storage-linked-service).

### Társított szolgáltatás létrehozása az Azure SQL Database-hez
1. A **Data Factory Editorban** kattintson az eszköztár **Új adattár** gombjára, és a legördülő menüben válassza az **Azure SQL Database** lehetőséget. A jobb oldali panelen megjelenik az Azure SQL társított szolgáltatás létrehozására szolgáló JSON-sablon.
2. Cserélje le a `<servername>`, `<databasename>`, `<username>@<servername>` és `<password>` paraméter értékét az Azure SQL-kiszolgáló, az adatbázis és a felhasználói fiók nevére, valamint a felhasználói fiók jelszavára. 
3. Kattintson az eszköztár **Üzembe helyezés** gombjára az **AzureSqlLinkedService** létrehozásához és üzembe helyezéséhez.
4. Győződjön meg arról, hogy az **AzureSqlLinkedService** szolgáltatás megjelenik a fanézetben. 

> [AZURE.NOTE]
> A JSON-tulajdonságokkal kapcsolatos információk: [Adatok áthelyezése SQL Database-ből vagy SQL Database-be](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).

## Adatkészletek létrehozása
Az előző lépésben létrehozta az **AzureStorageLinkedService** és az **AzureSqlLinkedService** társított szolgáltatásokat egy Azure Storage-fiók és egy Azure SQL Database az **ADFTutorialDataFactoryPSH** data factoryhez való társításához. Ebben a lépésben két adatkészletet határoz meg – az **InputDataset** és az **OutputDataset** adatkészletet –, amelyek az AzureStorageLinkedService és az AzureSqlLinkedService szolgáltatás által hivatkozott bemeneti és kimeneti adatokat jelölik. Az InputDataset adatkészlethez megadja a forrásadatokkal rendelkező blobot tároló blobtárolót, az OutputDataset adatkészlethez pedig megadja a kimeneti adatokat tároló SQL-táblát. 

### Bemeneti adatkészlet létrehozása 
Ebben a lépésben létrehozza az **InputDataset** nevű adatkészletet, amely egy, az Azure Storage-ben található blob-tárolóra mutat, amelyet az **AzureStorageLinkedService** társított szolgáltatás jelöl.

1. A Data Factory **szerkesztőjében** kattintson a **... Továbbiak**, majd az **Új adatkészlet**, végül az **Azure Blob Storage** elemre a legördülő menüben. 

    ![Új adatkészlet menü](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. A jobb oldali panelen cserélje le a JSON-t a következő JSON-kódrészletre: 

        {
          "name": "InputDataset",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
              "folderPath": "adftutorial/",
              "fileName": "emp.txt",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }
        
     Vegye figyelembe a következő szempontokat: 
    
    - Az adatkészlet **típusa** **AzureBlob** értékre van állítva.
    - A **linkedServiceName** tulajdonság **AzureStorageLinkedService** értékre van állítva. Ezt a társított szolgáltatást a 2. lépésben hozta létre.
    - A **folderPath** (mappaútvonal) tulajdonság az **adftutorial** tárolóra van állítva. A blob nevét is megadhatja a mappán belül a **fileName** tulajdonsággal. Mivel itt nincs megadva a blob neve, a tárolóban lévő összes blob adata bemeneti adatnak minősül.  
    - A formátum **type** (típus) tulajdonsága **TextFormat** (Szövegformátum) értékre van állítva.
    - A szövegfájlban két mező található – a **FirstName** (Utónév) és a **LastName** (Vezetéknév) –, amelyeket egy vessző karakter választ el (**columnDelimiter**). 
    - Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre). Így a Data Factory szolgáltatás óránként keres bemeneti adatokat a megadott blobtároló (**adftutorial**) gyökérmappájában. 
    
    Ha nem adja meg a **fileName** paramétert a **bemeneti** adatkészlethez, a bemeneti mappában (**folderPath**) található összes fájl/blob bemenetnek minősül. Ha megadja a fileName paramétert a JSON-fájlban, csak a megadott fájl/blob minősül bemenetnek.
 
    Ha nem adja meg a **fileName** (fájlnév) paramétert a **kimeneti táblához**, a **folderPath** útvonalon előállított fájlok a következő formátumban lesznek elnevezve: &lt;Guid\&gt;.txt (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Ha a **folderPath** és a **fileName** tulajdonságokat dinamikusan szeretné beállítani a **SliceStart** változó időpontja alapján, használja a **partitionedBy** tulajdonságot. A következő példában a folderPath tulajdonság a SliceStart (a feldolgozás alatt álló szelet kezdő időpontja) változó Év, Hónap és Nap értékeit, a fileName tulajdonság pedig a SliceStart változó Óra értékét használja. Ha például a szelet előállítása a 2016-09-20T08:00:00 időpontban kezdődik, a folderName tulajdonság beállítása wikidatagateway/wikisampledataout/2016/09/20, a fileName beállítása pedig 08.csv lesz. 

            "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": 
            [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
            ],
2. Kattintson az eszköztár **Üzembe helyezés** gombjára az **InputDataset** adatkészlet létrehozásához és üzembe helyezéséhez. Győződjön meg arról, hogy az **InputDataset** adatkészlet megjelenik a fanézetben.

> [AZURE.NOTE]
> A JSON-tulajdonságokkal kapcsolatos információk: [Adatok áthelyezése Azure Blobból vagy Azure Blobba](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).

### Kimeneti adatkészlet létrehozása
A lépés ezen részében egy kimeneti adatkészletet hoz létre **OutputDataset** néven. Ez az adathalmaz egy SQL-táblára mutat abban az Azure SQL Database-adatbázisban, amelyet az **AzureSqlLinkedService** jelöl. 

1. A Data Factory **szerkesztőjében** kattintson a **... Továbbiak**, majd az **Új adatkészlet**, végül az **Azure SQL** elemre a legördülő menüben. 
2. A jobb oldali panelen cserélje le a JSON-t a következő JSON-kódrészletre:

        {
          "name": "OutputDataset",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }
        
     Vegye figyelembe a következő szempontokat: 
    
    - Az adatkészlet **type** (típus) tulajdonsága **AzureSQLTable** értékre van állítva.
    - A **linkedServiceName** tulajdonság az **AzureSqlLinkedService** értékre van állítva (ezt a társított szolgáltatást a 2. lépésben hozta létre).
    - A **tablename** tulajdonság **emp** értékre van állítva.
    - Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName**. Az ID azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.
    - Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre).  A Data Factory szolgáltatás óránként előállít egy kimeneti adatszeletet az Azure SQL Database **emp** táblájában.

3. Kattintson az eszköztár **Üzembe helyezés** gombjára az **OutputDataset** adatkészlet létrehozásához és üzembe helyezéséhez. Győződjön meg arról, hogy az **OutputDataset** adatkészlet megjelenik a fanézetben. 

> [AZURE.NOTE]
> A JSON-tulajdonságokkal kapcsolatos információk: [Adatok áthelyezése SQL Database-ből vagy SQL Database-be](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).

## Folyamat létrehozása
Ebben a lépésben létrehoz egy **másolási tevékenységgel** rendelkező folyamatot, amely bemenetként az **InputDataset**, kimenetként pedig az **OutputDataset** adatkészletet használja.

1. A Data Factory **szerkesztőjében** kattintson a **... Továbbiak**, majd az **Új adatcsatorna** elemre. Azt is megteheti, hogy a jobb gombbal a **Pipelines** (Folyamatok) elemre kattint a fanézetben, majd a **New pipeline** (Új folyamat) parancsot választja.
2. A jobb oldali panelen cserélje le a JSON-t a következő JSON-kódrészletre: 
        
        {
          "name": "ADFTutorialPipeline",
          "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
              {
                "name": "CopyFromBlobToSQL",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "InputDataset"
                  }
                ],
                "outputs": [
                  {
                    "name": "OutputDataset"
                  }
                ],
                "typeProperties": {
                  "source": {
                    "type": "BlobSource"
                  },
                  "sink": {
                    "type": "SqlSink",
                    "writeBatchSize": 10000,
                    "writeBatchTimeout": "60:00:00"
                  }
                },
                "Policy": {
                  "concurrency": 1,
                  "executionPriorityOrder": "NewestFirst",
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2016-07-12T00:00:00Z",
            "end": "2016-07-13T00:00:00Z"
          }
        } 

    Vegye figyelembe a következő szempontokat:

    - A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **Copy** értékre van beállítva.
    - A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**.
    - A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva.

    A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra. Azt is megteheti, hogy a dátum-időpont paraméternek csak a dátum részét adja meg, az időpont részét pedig kihagyja. Megadhatja például a „2016-02-03” értéket, amely a következőnek felel meg: „2016-02-03T00:00:00Z”
    
    Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2016-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. 
    
    Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz.
    
    Az előző példában 24 adatszelet van, mert a rendszer óránként létrehoz egy adatszeletet.
    
4. Kattintson az eszköztár **Deploy** (Üzembe helyezés) gombjára az **ADFTutorialPipeline** létrehozásához és üzembe helyezéséhez. Győződjön meg arról, hogy a folyamat megjelenik a fanézetben. 
5. Most pedig az **X**-re kattintva zárja be az **Editor** (Szerkesztő) panelt. Kattintson újra az **X-re** az **ADFTutorialDataFactory** **Data Factory**-kezdőlapjának megjelenítéséhez.

**Gratulálunk!** Sikeresen létrehozott egy Azure data factoryt, társított szolgáltatásokat, táblákat és egy folyamatot, valamint ütemezte is a folyamatot.   
 
### A data factory megtekintése diagramnézetben 
1. A **Data Factory** panelen kattintson a **Diagram** elemre.

    ![Data Factory panel – Diagram csempe](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Az alábbi képhez hasonló diagramnak kell megjelennie: 

    ![Diagramnézet](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)

    Nagyíthat, kicsinyíthet, 100%-ra vagy mérethez igazítva nagyíthat/kicsinyíthet, automatikusan elhelyezheti a folyamatokat és táblákat, illetve megjelenítheti a leszármaztatási információkat (a kijelölt elemek fölérendelt vagy alárendelt elemeinek kiemelésével).  Ha duplán kattint egy objektumra (bemeneti/kimeneti táblára, illetve folyamatra), megtekintheti a tulajdonságait. 
3. A diagramnézetben kattintson a jobb gombbal az **ADFTutorialPipeline** folyamatra, majd kattintson az **Open pipeline** (Folyamat megnyitása) elemre. 

    ![Folyamat megnyitása](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)
4. Ekkor meg kell jelennie a folyamat tevékenységeinek, valamint a tevékenységek bemeneti és kimeneti adatkészleteinek. Ebben az oktatóanyagban csak egy tevékenység (a másolási tevékenység) szerepel a folyamatban, és a tevékenység bemeneti adatkészlete az InputDataset, a kimeneti adatkészlete pedig az OutputDataset.   

    ![Megnyitott folyamatnézet](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenedPipeline.png)
5. A diagramnézethez való visszatéréshez kattintson a **Data factory** elemre a bal felső sarokban lévő navigációs menüben. A diagramnézet az összeg folyamatot megjeleníti. Ebben a példában csak egy folyamatot hozott létre.   
 

## Folyamat figyelése
Ebben a lépésben az Azure Portal használatával figyeli egy Azure data factory eseményeit. 

### Folyamat figyelése diagramnézetben

1. Kattintson az **X-re** a **Diagram** nézet bezárásához és a Data Factory kezdőlapjának megjelenítéséhez. Ha bezárta a webböngészőt, tegye a következőket: 
    2. Nyissa meg az [Azure Portalt](https://portal.azure.com/). 
    2. Kattintson duplán az **ADFTutorialDataFactory** elemre a **Kezdőpulton** (vagy) kattintson az **Adat-előállítók** elemre a bal oldali menüben és keresse meg az ADFTutorialDataFactory elemet. 
3. A panelen megjelenik a létrehozott táblák és folyamatok száma és neve.

    ![kezdőlap a nevekkel](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png)
4. Most kattintson a **Datasets** (Adatkészletek) csempére.
5. Az **Adatkészletek** panelen kattintson az **InputDataset** adatkészletre. Ez az adatkészlet az **ADFTutorialPipeline** bementi adatkészlete.

    ![Adatkészletek kiválasztott InputDataset elemmel](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Az összes adatszelet megjelenítéséhez kattintson a három pontot ábrázoló gombra (**...**).

    ![Összes bemeneti adatszelet](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  

    Figyelje meg, hogy az aktuális időpontig már létrejöttek az adatszeletek, és hogy **Kész** állapotúak, mert az **emp.txt** fájl folyamatosan megtalálható a következő blobtárolóban: **adftutorial\input**. Győződjön meg arról, hogy a panel alsó részén található **Recently failed slices** (Legutóbbi sikertelen szeletek) részben egy szelet sem jelenik meg.

    Mind a **Recently updated slices** (A legutóbb frissített szeletek), mind pedig a **Recently failed slices** (Legutóbbi sikertelen szeletek) lista a **LAST UPDATE TIME** (LEGUTÓBBI FRISSÍTÉS IDŐPONTJA) szerint van rendezve. 
    
    A szeletek szűréséhez kattintson az eszköztár **Filter** (Szűrés) elemére.  
    
    ![Bementi szeletek szűrése](./media/data-factory-copy-activity-tutorial-using-azure-portal/filter-input-slices.png)
6. Zárja be a paneleket, amíg el nem jut az **Adatkészletek** panelhez. Kattintson az **OutputDataset** adatkészletre. Ez az adatkészlet az **ADFTutorialPipeline** kimenti adatkészlete.

    ![Datasets (Adatkészletek) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png)
6. Az **OutputDataset** panelnek a következő képen látható módon kell megjelennie:

    ![Table (Tábla) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png) 
7. Figyelje meg, hogy az aktuális időpontig létrejöttek az adatszeletek, és hogy **Ready** (Kész) állapotúak. A panel alsó részén található **Problem slices** (Problémás szeletek) részben egy szelet sem jelenik meg.
8. Az összes szelet megjelenítéséhez kattintson a három pontot ábrázoló gombra (**...**).

    ![Data slices (Adatszeletek) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png)
9. Kattintson a listában lévő bármelyik adatszeletre, és megjelenik az **Adatszelet** panel.

    ![Data slice (Adatszelet) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
  
    Ha a szelet nem **Ready** (Kész) állapotú, az **Upstream slices that are not ready** (Nem kész állapotú fölérendelt szeletek) listában láthatja azokat a nem kész állapotú fölérendelt szeleteket, amelyek blokkolják az aktuális szelet végrehajtását.
11. A **DATA SLICE** (ADATSZELET) panel alsó részén található listában az összes tevékenységfuttatás megjelenik. Kattintson egy **tevékenységfuttatásra** az **Tevékenységfuttatás részletei** panel megjelenítéséhez. 

    ![Az Activity Run Details (Tevékenységfuttatás részletei) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)
12. Kattintson az **X**-re az összes panel bezárásához, amíg vissza nem tér az **ADFTutorialDataFactory** kezdőpaneljére.
14. (opcionális) Kattintson az **ADFTutorialDataFactory** kezdőlapjának **Pipelines** (Folyamatok) elemére, kattintson az **ADFTutorialPipeline** elemre a **Pipelines** (Folyamatok) panelen, és jelenítse meg a bemeneti táblák (**Consumed** (Felhasznált)) vagy a kimeneti táblák (**Produced** (Létrehozott)) részleteit.
15. Indítsa el az **SQL Server Management Studiót**, csatlakozzon az Azure SQL Database-hez, és ellenőrizze, hogy a sorok megjelennek-e az adatbázis **emp** táblájában.

    ![SQL-lekérdezés eredményei](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

### Folyamat figyelése a Monitor & Manage alkalmazással
A folyamatok figyeléséhez a Monitor & Manage alkalmazást is használhatja. Az alkalmazás használatával kapcsolatos részletes információkért tekintse meg a [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App](data-factory-monitor-manage-app.md) (Azure Data Factory-folyamatok figyelése és felügyelete a Monitoring and Management használatával) című cikket.

1. Kattintson a **Monitor & Manage** csempére a Data Factory kezdőlapján.

    ![Monitor & Manage csempe](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Meg kell jelennie a **Monitor & Manage alkalmazásnak**. Módosítsa a **kezdési idő** és a **befejezési idő** értékét, hogy megfeleljen a folyamat kezdési (2016-07-12) és befejezési (2016-07-13) idejének, és kattintson az **Alkalmaz** gombra. 

    ![Monitor & Manage alkalmazás](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png) 
3. Válasszon egy tevékenységablakot az **Activity Windows** (Tevékenységablakok) listában a részleteinek a megtekintéséhez. 
    ![Tevékenységablakok részletei](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

## Összefoglalás 
Az oktatóanyag során létrehozott egy Azure data factoryt, hogy adatokat másoljon egy Azure-blobból egy Azure SQL Database-adatbázisba. Az Azure Portalt használta a data factory, a társított szolgáltatások, az adatkészletek és a folyamat létrehozásához. Az oktatóanyag során a következő főbb lépéseket végezte el:  

1.  Létrehozott egy Azure **data factoryt**.
2.  **Társított szolgáltatásokat** hozott létre:
    1. Egy **Azure Storage** társított szolgáltatást a bemeneti adatokat tároló Azure Storage-fiók társításához.    
    2. Egy **Azure SQL** társított szolgáltatást a kimeneti adatokat tároló Azure SQL Database társításához. 
3.  **Adatkészleteket** hozott létre, amelyek a folyamat bemeneti és kimeneti adatait írják le.
4.  Létrehozott egy **folyamatot** egy **Másolási tevékenységgel**, ahol a **BlobSource** a forrás, az **SqlSink** pedig a fogadó.  


## Lásd még:
| Témakör | Leírás |
| :---- | :---- |
| [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) | Ez a cikk részletes információkat tartalmaz a jelen oktatóanyagban használt Másolás tevékenységről. |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) | Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok](data-factory-create-pipelines.md) | Ennek a cikknek a segítségével megismerheti a folyamatokat és tevékenységeket az Azure Data Factoryban. |
| [Adatkészletek](data-factory-create-datasets.md) | Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban.
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) | Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. 





<!--HONumber=Sep16_HO5-->


