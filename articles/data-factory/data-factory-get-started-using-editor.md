<properties 
    pageTitle="Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Data Factory Editor használatával" 
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
    ms.date="05/16/2016" 
    ms.author="spelluru"/>

# Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Data Factory Editor használatával
> [AZURE.SELECTOR]
- [Az oktatóanyag áttekintése](data-factory-get-started.md)
- [A Data Factory Editor használata](data-factory-get-started-using-editor.md)
- [A Visual Studio használata](data-factory-get-started-using-vs.md)
- [A PowerShell használata](data-factory-monitor-manage-using-powershell.md)
- [A Másolás varázsló használata](data-factory-copy-data-wizard-tutorial.md)


Ez az oktatóanyag a következő lépéseket tartalmazza:

Lépés | Leírás
-----| -----------
[Azure data factory létrehozása](#create-data-factory) | Ebben a lépésben létrehozza az **ADFTutorialDataFactory** nevű Azure data factoryt.  
[Társított szolgáltatások létrehozása](#create-linked-services) | Ebben a lépésben létrehozza a következő két társított szolgáltatást: **StorageLinkedService** és **AzureSqlLinkedService**. Az AzureStorageLinkedService az Azure-tárolót, az AzureSqlLinkedService pedig az Azure SQL Database-t társítja az ADFTutorialDataFactory data factoryhez. A folyamat bemeneti adatai az Azure Blob Storage egyik blobtárolójában, a kimeneti adatok pedig az Azure SQL Database egyik táblájában találhatók. Ezért ezt a két adattárat társított szolgáltatásként kell hozzáadnia a data factoryhez.      
[Bemeneti és kimeneti adatkészletek létrehozása](#create-datasets) | Az előző lépésben társított szolgáltatásokat hozott létre, amelyek a bemeneti/kimeneti adatokat tartalmazó adattárakra hivatkoznak. Ebben a lépésben két data factory-táblát határoz meg – az **EmpTableFromBlob** és az **EmpSQLTable** táblát –, amelyek az adattárakban tárolt bemeneti/kimeneti adatokat képviselik. Az EmpTableFromBlob táblához meg fogja adni a forrásadatokkal rendelkező blobot tároló blobtárolót, az EmpSQLTable táblához pedig meg fogja adni a kimeneti adatokat tároló SQL-táblát. Egyéb tulajdonságokat is meg fog adni, például az adatok szerkezetét, rendelkezésre állását stb. 
[Folyamat létrehozása](#create-pipeline) | Ebben a lépésben létrehozza az **ADFTutorialPipeline** nevű folyamatot az ADFTutorialDataFactory data factoryben. A folyamat része lesz egy **Másolási tevékenység**, amely bemeneti adatokat másol az Azure blobból a kimeneti Azure SQL-táblába. A Másolás tevékenység végzi az adattovábbítást az Azure Data Factoryben, és a tevékenységet egy olyan, globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és méretezhető módon másolja az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket. 
[Folyamat figyelése](#monitor-pipeline) | Ebben a lépésben a bemeneti és kimeneti táblák szeleteit figyeli az Azure Portal használatával.

> [AZURE.IMPORTANT] Tekintse meg [Az oktatóanyag áttekintése](data-factory-get-started.md) című cikket, és az oktatóanyag elvégzése előtt hajtsa végre az előfeltételként felsorolt lépéseket.

## Data factory létrehozása
Ebben a lépésben az Azure portál használatával létrehozza az **ADFTutorialDataFactory** nevű Azure data factoryt.

1.  Miután bejelentkezett az [Azure Portalra][azure-portal], kattintson a ** NEW** (ÚJ) elemre a bal alsó sarokban, válassza a **Data analytics** (Adatelemzés) lehetőséget a **Create** (Létrehozás) panelen, és kattintson a **Data Factory** elemre a **Data analytics** (Adatelemzés) panelen. 

    ![New (Új)->DataFactory][image-data-factory-new-datafactory-menu]    

6. A **New data factory** (Új data factory) panelen:
    1. A**Name** (Név) mezőbe írja be a következőt: **ADFTutorialDataFactory**. 
    
        ![A New data factory (Új data factory) panel][image-data-factory-getstarted-new-data-factory-blade]
    2. Kattintson a **RESOURCE GROUP NAME** (ERŐFORRÁSCSOPORT NEVE) elemre, és tegye a következőket:
        1. Kattintson a **Create a new resource group** (Új erőforráscsoport létrehozása) elemre.
        2. A **Create resource group** (Erőforráscsoport létrehozása) panelen az erőforráscsoport **neveként** adja meg az **ADFTutorialResourceGroup** kifejezést, és kattintson az **OK** gombra. 

            ![Create resource group (Erőforráscsoport létrehozása)][image-data-factory-create-resource-group]

        Az oktatóanyag egyes lépései azt feltételezik, hogy az **ADFTutorialResourceGroup** nevet adta az erőforráscsoportnak. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
7. Győződjön meg arról, hogy a **New data factory** (Új data factory) panelen be van jelölve az **Add to Startboard** (Hozzáadás a kezdőpulthoz) beállítás.
8. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.

    Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a **Data factory name “ADFTutorialDataFactory” is not available** (Az „ADFTutorialDataFactory” data factory-név nem érhető el) hibaüzenetet kapja, módosítsa a data factory nevét (például sajátnévADFTutorialDataFactory-re), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.  
     
    ![A Data Factory name not available (A data factory neve nem érhető el) üzenet][image-data-factory-name-not-available]
    
    > [AZURE.NOTE] A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.  
    > 
    > Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.

9. Kattintson a bal oldali **NOTIFICATIONS** (ÉRTESÍTÉSEK) központra, és keresse meg a létrehozási folyamat értesítéseit. Ha a **NOTIFICATIONS** (ÉRTESÍTÉSEK) panel meg van nyitva, kattintson az **X**-re a bezárásához. 
10. A létrehozás befejezése után a **DATA FACTORY** panel az alábbiak szerint jelenik meg.

    ![Data factory kezdőlap][image-data-factory-get-stated-factory-home-page]

## Társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Az adattárak lehetnek Azure Storage-tárolók, Azure SQL Database-adatbázisok vagy helyszíni SQL Server-adatbázisok.

Ebben a lépésben létrehozza a következő két társított szolgáltatást: **StorageLinkedService** és **AzureSqlLinkedService**. Az AzureStorageLinkedService társított szolgáltatás egy Azure Storage-fiókot, az AzureSqlLinkedService pedig egy Azure SQL Database-adatbázist társít az **ADFTutorialDataFactory** data factoryhez. Az oktatóanyag későbbi részében létre fog hozni egy folyamatot, amely adatokat másol az AzureStorageLinkedService blobtárolójából az AzureSqlLinkedService SQL-táblájába.

### Társított szolgáltatás létrehozása az Azure Storage-fiókhoz
1.  A **DATA FACTORY** panelen kattintson az **Author and deploy** (Fejlesztés és üzembe helyezés) csempére a data factory **szerkesztőjének** elindításához.

    ![Az Author and deploy (Fejlesztés és üzembe helyezés) csempe][image-author-deploy-tile] 

     
5. A **szerkesztőben** kattintson az eszköztár **New data store** (Új adattár) gombjára, és a legördülő menüben válassza az **Azure Storage** lehetőséget. A jobb oldali panelen megjelenik az Azure Storage társított szolgáltatás létrehozására szolgáló JSON-sablon. 

    ![A szerkesztő New data store (Új adattár) gombja][image-editor-newdatastore-button]
    
6. Az **accountname** és az **accountkey**kifejezéseket cserélje az Azure-tárfiókja nevére, illetve kulcsára. 

    ![Szerkesztő – A blobtároló JSON-fájlja](./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-json.png)    
    
    A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) (Referencia a JSON-parancsprogramokhoz).

6. Az AzureStorageLinkedService üzembe helyezéséhez kattintson az eszköztár **Deploy** (Üzembe helyezés) gombjára. Győződjön meg arról, hogy a címsorban megjelenik a **LINKED SERVICE CREATED SUCCESSFULLY** (A TÁRSÍTOTT SZOLGÁLTATÁS SIKERESEN LÉTREJÖTT) üzenet.

    ![Szerkesztő – A blobtároló üzembe helyezése][image-editor-blob-storage-deploy]

### Társított szolgáltatás létrehozása az Azure SQL Database-hez
1. A **Data Factory Editorban** kattintson az eszköztár **New data store** (Új adattár) gombjára, és a legördülő menüben válassza az **Azure SQL database** lehetőséget. A jobb oldali panelen megjelenik az Azure SQL társított szolgáltatás létrehozására szolgáló JSON-sablon.

    ![Szerkesztő – Azure SQL-beállítások][image-editor-azure-sql-settings]

2. A **servername**, **databasename**, **username@servername** és **password** paraméterek értékét cserélje le az Azure SQL-kiszolgáló, az adatbázis és a felhasználói fiók nevére, valamint a felhasználói fiók jelszavára. 
3. Kattintson az eszköztár **Deploy** (Üzembe helyezés) gombjára az AzureSqlLinkedService létrehozásához és üzembe helyezéséhez. 
   

## Adatkészletek létrehozása
Az előző lépésben létrehozta az **AzureStorageLinkedService** és az **AzureSqlLinkedService** társított szolgáltatásokat egy Azure Storage-fiók és egy Azure SQL Database az **ADFTutorialDataFactoryPSH** data factoryhez való társításához. Ebben a lépésben két data factory-táblát határoz meg – az **EmpTableFromBlob** és az **EmpSQLTable** táblákat –, amelyek az adattárakban tárolt, az AzureStorageLinkedService és az AzureSqlLinkedService szolgáltatások által hivatkozott bemeneti és kimeneti adatokat jelölik. Az EmpTableFromBlob táblához meg fogja adni a forrásadatokkal rendelkező blobot tároló blobtárolót, az EmpSQLTable táblához pedig meg fogja adni a kimeneti adatokat tároló SQL-táblát. 

### Bemeneti adatkészlet létrehozása 
A tábla egy sémával rendelkező, téglalap alakú adatkészlet. Ebben a lépésben létrehozza az **EmpBlobTable** nevű táblát, amely az **AzureStorageLinkedService** társított szolgáltatás által jelölt Azure Storage-blobtárolójára mutat.

1. A Data Factory **szerkesztőjében** kattintson az eszköztár **New dataset** (Új adatkészlet) gombjára, és a legördülő menüben válassza a **Blob table** (Blob-tábla) lehetőséget. 
2. A jobb oldali panelen cserélje le a JSON-t a következő JSON-kódrészletre: 

        {
          "name": "EmpTableFromBlob",
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

        
     Vegye figyelembe a következőket: 
    
    - Az adatkészlet **típusa** **AzureBlob** értékre van állítva.
    - A **linkedServiceName** tulajdonság **AzureStorageLinkedService** értékre van állítva. Ezt a társított szolgáltatást a 2. lépésben hozta létre.
    - A **folderPath** (mappaútvonal) tulajdonság az **adftutorial** tárolóra van állítva. A blob nevét is megadhatja a mappán belül. Mivel itt nincs megadva a blob neve, a tárolóban lévő összes blob adata bemeneti adatnak minősül.  
    - A formátum **type** (típus) tulajdonsága **TextFormat** (Szövegformátum) értékre van állítva.
    - A szövegfájlban két mező található – a **FirstName** (Utónév) és a **LastName** (Vezetéknév) –, amelyeket egy vessző karakter választ el (**columnDelimiter**). 
    - Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre), így a Data Factory szolgáltatás óránként keres bemeneti adatokat a megadott blobtároló (**adftutorial**) gyökérmappájában. 
    

    Ha nem adja meg a **fileName** (fájlnév) paramétert a **bemeneti** **táblához**, a bemeneti mappában (**folderPath**) lévő összes fájl/blob bemenetnek minősül. Ha megadja a fileName paramétert a JSON-fájlban, csak a megadott fájl/blob minősül bemenetnek.
 
    Ha nem adja meg a **fileName** (fájlnév) paramétert a **kimeneti táblához**, a **folderPath** útvonalon előállított fájlok a következő formátumban lesznek elnevezve: &lt;Guid\&gt;.txt (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Ha a **folderPath** és a **fileName** tulajdonságokat dinamikusan szeretné beállítani a **SliceStart** változó időpontja alapján, használja a **partitionedBy** tulajdonságot. A következő példában a folderPath tulajdonság a SliceStart (a feldolgozás alatt álló szelet kezdő időpontja) változó Év, Hónap és Nap értékeit, a fileName tulajdonság pedig a SliceStart változó Óra értékét használja. Ha például a szelet előállítása a 2014-10-20T08:00:00 időpontban kezdődik, a folderName tulajdonság beállítása wikidatagateway/wikisampledataout/2014/10/20, a fileName beállítása pedig 08.csv lesz. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

    A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) (Referencia a JSON-parancsprogramokhoz).

2. Kattintson az eszköztár **Deploy** (Üzembe helyezés) gombjára az **EmpTableFromBlob** tábla létrehozásához és üzembe helyezéséhez. Győződjön meg arról, hogy a szerkesztő címsorában megjelenik a **TABLE CREATED SUCCESSFULLY** (A TÁBLA SIKERESEN LÉTREJÖTT) üzenet.

### Kimeneti adatkészlet létrehozása
A lépés ezen szakaszában létrehozza az **EmpSQLTable** nevű kimeneti táblát, amely az Azure SQL Database szolgáltatásnak az **AzureSqlLinkedService** társított szolgáltatás által jelölt SQL-táblájára mutat. 

1. A Data Factory **szerkesztőjében** kattintson az eszköztár **New dataset** (Új adatkészlet) gombjára, és a legördülő menüben válassza az **Azure SQL table** (Azure SQL-tábla) lehetőséget. 
2. A jobb oldali panelen cserélje le a JSON-t a következő JSON-kódrészletre:

        {
          "name": "EmpSQLTable",
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

        
     Vegye figyelembe a következőket: 
    
    * Az adatkészlet **type** (típus) tulajdonsága **AzureSQLTable** értékre van állítva.
    * A **linkedServiceName** tulajdonság **AzureSqlLinkedService** értékre van állítva (ezt a társított szolgáltatást a 2. lépésben hozta létre).
    * A **tablename** tulajdonság **emp** értékre van állítva.
    * Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName** –, az ID azonban azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.
    * Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre).  A Data Factory szolgáltatás óránként előállít egy kimeneti adatszeletet az Azure SQL Database **emp** táblájában.


3. Kattintson az eszköztár **Deploy** (Üzembe helyezés) elemére az **EmpSQLTable** tábla létrehozásához és üzembe helyezéséhez.


## Folyamat létrehozása
Ebben a lépésben létrehoz egy **Másolási tevékenységgel** rendelkező folyamatot, amely bemenetként az **EmpTableFromBlob**, kimenetként pedig az **EmpSQLTable** táblát használja.

1. A Data Factory **szerkesztőjében** kattintson az eszköztár **New pipeline** (Új folyamat) gombjára. Ha nem látja ezt a gombot, kattintson a három pontot ** (...)** ábrázoló gombra. Azt is megteheti, hogy a jobb gombbal a **Pipelines** (Folyamatok) elemre kattint a fanézetben, majd a **New pipeline** (Új folyamat) parancsot választja.

    ![Szerkesztő – New pipeline (Új folyamat) gomb][image-editor-newpipeline-button]
 
2. A jobb oldali panelen cserélje le a JSON-t a következő JSON-kódrészletre: 
        
        {
          "name": "ADFTutorialPipeline",
          "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
              {
                "name": "CopyFromBlobToSQL",
                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "EmpTableFromBlob"
                  }
                ],
                "outputs": [
                  {
                    "name": "EmpSQLTable"
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
            "start": "2015-07-12T00:00:00Z",
            "end": "2015-07-13T00:00:00Z"
          }
        } 

    Vegye figyelembe a következőket:

    - A tevékenységek szakaszban csak egyetlen olyan tevékenység van, amelynek a **type** (típus) tulajdonsága **CopyActivity** értékre van állítva.
    - A tevékenység bemenetének beállítása **EmpTableFromBlob**, a kimeneté pedig **EmpSQLTable**.
    - Az **átalakítás** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva.

    A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra. Azt is megteheti, hogy a dátum-időpont paraméternek csak a dátum részét adja meg, az időpont részét pedig kihagyja. Megadhatja például a „2015-02-03” értéket, amely a következőnek felel meg: „2015-02-03T00:00:00Z”.
    
    Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2014-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. 
    
    Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz.
    
    A fenti példában 24 adatszelet lesz, mert a rendszer óránként létrehoz egy adatszeletet.
    
    A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) (Referencia a JSON-parancsprogramokhoz).

4. Kattintson az eszköztár **Deploy** (Üzembe helyezés) gombjára az **ADFTutorialPipeline** létrehozásához és üzembe helyezéséhez. Győződjön meg arról, hogy megjelenik a **PIPELINE CREATED SUCCESSFULLY** (A FOLYAMAT SIKERESEN LÉTREJÖTT) üzenet.
5. Most pedig az **X**-re kattintva zárja be az **Editor** (Szerkesztő) panelt. Kattintson ismét az **X**-re az eszköztárat és fanézetet tartalmazó ADFTutorialDataFactory panel bezárásához. Ha a **Your unsaved edits will be discarded** (A nem mentett módosítások elvesznek) üzenet jelenik meg, kattintson az **OK** gombra.
6. Az **ADFTutorialDataFactory** **DATA FACTORY** paneljének kell megjelennie.

**Gratulálunk!** Sikeresen létrehozott egy Azure data factoryt, társított szolgáltatásokat, táblákat és egy folyamatot, valamint ütemezte is a folyamatot.   
 
### A data factory megtekintése diagramnézetben 
1. A **DATA FACTORY** panelen kattintson a **Diagram** elemre.

    ![Data Factory panel – Diagram csempe][image-datafactoryblade-diagramtile]

2. Az alábbihoz hasonló diagramnak kell megjelennie: 

    ![Diagramnézet][image-data-factory-get-started-diagram-blade]

    Nagyíthat, kicsinyíthet, 100%-ra vagy mérethez igazítva nagyíthat/kicsinyíthet, automatikusan elhelyezheti a folyamatokat és táblákat, illetve megjelenítheti a leszármaztatási információkat (a kijelölt elemek fölérendelt vagy alárendelt elemeinek kiemelésével).  Ha duplán kattint egy objektumra (bemeneti/kimeneti táblára, illetve folyamatra), megtekintheti a tulajdonságait. 
3. A diagramnézetben kattintson a jobb gombbal az **ADFTutorialPipeline** folyamatra, majd kattintson az **Open pipeline** (Folyamat megnyitása) elemre. Ekkor meg kell jelennie a folyamat tevékenységeinek, valamint a tevékenységek bemeneti és kimeneti adatkészleteinek. Ebben az oktatóanyagban csak egy tevékenység (a Másolási tevékenység) szerepel a folyamatban, és a tevékenység bemeneti adatkészlete az EmpTableBlob, a kimeneti adatkészlete pedig az EmpSQLTable.   

    ![Folyamat megnyitása](./media/data-factory-get-started-using-editor/DiagramView-OpenPipeline.png)

4. A diagramnézethez való visszatéréshez kattintson a **Data factory** elemre a bal felső sarokban lévő navigációs menüben. A diagramnézet az összeg folyamatot megjeleníti. Ebben a példában csak egy folyamatot hozott létre.   
 

## Folyamat figyelése
Ebben a lépésben az Azure Portal használatával figyeli egy Azure data factory eseményeit. PowerShell-parancsmagokat is használhat az adatkészletek és folyamatok figyeléséhez. A figyeléshez használt parancsmagokkal kapcsolatos információkért tekintse meg a [Monitor and Manage Data Factory using PowerShell Cmdlets][monitor-manage-using-powershell] (Data Factoryk figyelése és felügyelete PowerShell-parancsmagok használatával) című cikket.

1. Ha még nincs megnyitva, lépjen az [Azure-portálra (előzetes verzió)][azure-portal]. 
2. Ha még nincs megnyitva az **ADFTutorialDataFactory** panelje, nyissa meg a **kezdőpulton** az **ADFTutorialDataFactory** elemre kattintva. 
3. A panelen megjelenik a létrehozott táblák és folyamatok száma és neve.

    ![kezdőlap a nevekkel][image-data-factory-get-started-home-page-pipeline-tables]

4. Most kattintson a **Datasets** (Adatkészletek) csempére.
5. A **Datasets** (Adatkészletek) panelen kattintson az **EmpTableFromBlob** elemre. Ez az **ADFTutorialPipeline** folyamat bemeneti táblája.

    ![A Datasets (Adatkészletek) panel az EmpTableFromBlob tábla kijelölésével][image-data-factory-get-started-datasets-emptable-selected]   
5. Figyelje meg, hogy az aktuális időpontig már létrejöttek az adatszeletek, és hogy **Ready** (Kész) állapotúak, mert az **emp.txt** fájl folyamatosan megtalálható a következő blobtárolóban: **adftutorial\input**. Győződjön meg arról, hogy a panel alsó részén található **Recently failed slices** (Legutóbbi sikertelen szeletek) részben egy szelet sem jelenik meg.

    Mind a **Recently updated slices** (A legutóbb frissített szeletek), mind pedig a **Recently failed slices** (Legutóbbi sikertelen szeletek) lista a **LAST UPDATE TIME** (LEGUTÓBBI FRISSÍTÉS IDŐPONTJA) szerint van rendezve. A szelet frissítési időpontja az alábbi esetekben változik. 
    

    -  Ha manuálisan frissíti a szelet állapotát, például a **Set-AzureRmDataFactorySliceStatus** parancsmag használatával, vagy ha a szelet **SLICE** (SZELET) panelén a **RUN** (FUTTATÁS) elemre kattint.
    -  Ha a szelet állapota egy végrehajtás (például egy futtatás kezdete, egy futtatás sikertelen befejezése, egy futtatás sikeres befejezése stb.) miatt megváltozik.
 
    Kattintson a listák címére vagy a folytatást jelző pontokra (**...**) a szeletek hosszabb listájának megtekintéséhez. A szeletek szűréséhez kattintson az eszköztár **Filter** (Szűrés) elemére.  
    
    Ha az adatszeleteket inkább a szelet kezdő/befejező időpontja szerint szeretné rendezni, kattintson a **Data slices (by slice time)** (Adatszeletek (a szelet időpontja szerint)) csempére.   

    ![A Data Slices by Slice Time (Adatszeletek a szelet időpontja szerint) csempe][DataSlicesBySliceTime]   

6. Most kattintson a **Datasets** (Adatkészletek) panelen az **EmpTableFromBlob** elemre. Ez az **ADFTutorialPipeline** folyamat kimeneti táblája.

    ![Datasets (Adatkészletek) panel][image-data-factory-get-started-datasets-blade]



     
6. Az **EmpSQLTable** panelnek az alábbi módon kell megjelennie:

    ![Table (Tábla) panel][image-data-factory-get-started-table-blade]
 
7. Figyelje meg, hogy az aktuális időpontig létrejöttek az adatszeletek, és hogy **Ready** (Kész) állapotúak. A panel alsó részén található **Problem slices** (Problémás szeletek) részben egy szelet sem jelenik meg.
8. Az összes szelet megjelenítéséhez kattintson a három pontot ábrázoló gombra (**...**).

    ![Data slices (Adatszeletek) panel][image-data-factory-get-started-dataslices-blade]

9. Kattintson a listában lévő bármelyik adatszeletre, és megjelenik a **DATA SLICE** (ADATSZELET) panel.

    ![Data slice (Adatszelet) panel][image-data-factory-get-started-dataslice-blade]
  
    Ha a szelet nem **Ready** (Kész) állapotú, az **Upstream slices that are not ready** (Nem kész állapotú fölérendelt szeletek) listában láthatja azokat a nem kész állapotú fölérendelt szeleteket, amelyek blokkolják az aktuális szelet végrehajtását. 

11. A **DATA SLICE** (ADATSZELET) panel alsó részén található listában az összes tevékenységfuttatás megjelenik. Kattintson egy **tevékenységfuttatásra** az **ACTIVITY RUN DETAILS** (TEVÉKENYSÉGFUTTATÁS RÉSZLETEI) panel megjelenítéséhez. 

    ![Az Activity Run Details (Tevékenységfuttatás részletei) panel][image-data-factory-get-started-activity-run-details]

    
12. Kattintson az **X**-re az összes panel bezárásához, amíg vissza nem tér az **ADFTutorialDataFactory** kezdőpaneljére.
14. (opcionális) Kattintson az **ADFTutorialDataFactory** kezdőlapjának **Pipelines** (Folyamatok) elemére, kattintson az **ADFTutorialPipeline** elemre a **Pipelines** (Folyamatok) panelen, és jelenítse meg a bemeneti táblák (**Consumed** (Felhasznált)) vagy a kimeneti táblák (**Produced** (Létrehozott)) részleteit.
15. Indítsa el az **SQL Server Management Studiót**, csatlakozzon az Azure SQL Database-hez, és ellenőrizze, hogy a sorok megjelennek-e az adatbázis **emp** táblájában.

    ![SQL-lekérdezés eredményei][image-data-factory-get-started-sql-query-results]


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
| [Folyamatok](data-factory-create-pipelines.md) | Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) | Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban.
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) | Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. 

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started-using-editor/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started-using-editor/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started-using-editor/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started-using-editor/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started-using-editor/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started-using-editor/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started-using-editor/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started-using-editor/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started-using-editor/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started-using-editor/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started-using-editor/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started-using-editor/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started-using-editor/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started-using-editor/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started-using-editor/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started-using-editor/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started-using-editor/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-get-started-using-editor/getstarted-data-factory-not-available.png
 


<!--HONumber=jun16_HO2-->


