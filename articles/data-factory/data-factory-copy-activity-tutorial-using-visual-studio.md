---
title: "Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Visual Studio használatával | Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan hozhat létre másolási tevékenységgel rendelkező Azure Data Factory-folyamatot a Visual Studio használatával."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b3381396ce198fbcaf13d63510ef12b225735a49


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Visual Studio használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és figyelhet meg Azure Data Factoryt a Visual Studióval. Az adat-előállítóban szereplő adatcsatorna másolási tevékenységgel másolja az adatokat az Azure Blob Storage-ből az Azure SQL Database-be.

Az oktatóanyag során a következő lépéseket fogja elvégezni:

1. Létrehozza a következő két társított szolgáltatást: **AzureStorageLinkedService1** és **AzureSqlinkedService1**. 
   
    Az AzureStorageLinkedService1 egy Azure Storage-ot, míg az AzureSqlLinkedService1 egy Azure SQL Database-t társít az **ADFTutorialDataFactoryVS** data factoryhoz. A folyamat bemeneti adatai az Azure Blob Storage egyik blobtárolójában, a kimeneti adatok pedig az Azure SQL Database egyik táblájában találhatók. Ezért ezt a két adattárat társított szolgáltatásként kell hozzáadnia a data factoryhez.
2. Létrehoz két adatkészletet: az **InputDataset** és az **OutputDataset** adatkészletet, amelyek az adattárakban tárolt bemeneti/kimeneti adatokat képviselik. 
   
    Az InputDataset adatkészlethez megadja a forrásadatokkal rendelkező blobot tároló blobtárolót. Az OutputDataset adatkészlethez megadja a kimeneti adatokat tároló SQL-táblát. Egyéb tulajdonságokat is megad, például a szerkezetet, rendelkezésre állást és a szabályzatot.
3. Létrehozza az **ADFTutorialPipeline** nevű folyamatot az ADFTutorialDataFactoryVS data factoryban. 
   
    A folyamat része egy **Másolási tevékenység**, amely bemeneti adatokat másol az Azure-blobból a kimeneti Azure SQL-táblába. A másolási tevékenység végzi az adattovábbítást az Azure Data Factoryban. A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket. 
4. Létrehoz egy **VSTutorialFactory** nevű data factoryt. Üzembe helyezi a data factoryt és a Data Factory-entitásokat (társított szolgáltatások, táblák és a folyamat).    

## <a name="prerequisites"></a>Előfeltételek
1. Olvassa el [Az oktatóanyag áttekintése](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikket, és hajtsa végre az **előfeltételként** felsorolt lépéseket. 
2. **Az Azure-előfizetés rendszergazdájának** kell lennie, hogy Data Factory-entitásokat tegyen közzé az Azure Data Factoryban.  
3. A számítógépre a következőket kell telepíteni: 
   * Visual Studio 2013 vagy Visual Studio 2015
   * Töltse le az Azure SDK-t a Visual Studio 2013-hoz vagy a Visual Studio 2015-höz. Nyissa meg az [Azure letöltési oldalát](https://azure.microsoft.com/downloads/), és kattintson a **VS 2013** vagy a **VS 2015** elemre a **.NET** szakaszban.
   * Töltse le a legújabb Azure Data Factory beépülő modult a Visual Studióhoz: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) vagy [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). A beépülő modult a következőképpen is frissítheti: A menüben kattintson a **Tools** -> **Extensions and Updates** -> **Online** -> **Visual Studio Gallery** -> **Microsoft Azure Data Factory Tools for Visual Studio** -> **Update** (Eszközök > Bővítmények és frissítések > Online > Visual Studio-gyűjtemény > Microsoft Azure Data Factory-eszközök a Visual Studióhoz > Frissítés) elemre.

## <a name="create-visual-studio-project"></a>Visual Studio-projekt létrehozása
1. Indítsa el a **Visual Studio 2013-at**. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre. Meg kell jelennie a **New project** (Új projekt) párbeszédpanelnek.  
2. A **New project** (Új projekt) párbeszédpanelen jelölje ki a **DataFactory** sablont, és kattintson az **Empty Data Factory Project** (Üres Data Factory-projekt) elemre. Ha nem látja a DataFactory sablont, zárja be a Visual Studiót, telepítse az Azure SDK-t a Visual Studio 2013-hoz, és nyissa meg újra a Visual Studiót.  
   
    ![A New project (Új projekt) párbeszédpanel](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Adja meg a projekt **nevét**, a **helyet**, valamint a **megoldás** nevét, és kattintson az **OK** gombra.
   
    ![Megoldáskezelő](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. A másolási tevékenység által támogatott forrásokért és fogadókért tekintse meg a [támogatott adattárak](data-factory-data-movement-activities.md##supported-data-stores-and-formats) című részt. A Data Factory által támogatott számítási szolgáltatások listájáért tekintse meg a [számítási társított szolgáltatások](data-factory-compute-linked-services.md) című részt. Az oktatóanyag során ne használjon számítási szolgáltatásokat. 

Ebben a lépésben létrehozza a következő két társított szolgáltatást: **AzureStorageLinkedService1** és **AzureSqlLinkedService1**. Az AzureStorageLinkedService1 társított szolgáltatás egy Azure Storage-fiókot, az AzureSqlLinkedService pedig egy Azure SQL Database adatbázist társít az **ADFTutorialDataFactory** data factoryhoz. 

### <a name="create-the-azure-storage-linked-service"></a>Az Azure Storage társított szolgáltatás létrehozása
1. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **Linked Services** (Társított szolgáltatások) elemre, mutasson az **Add** (Hozzáadás) parancsra, és kattintson a **New Item** (Új elem) elemre.      
2. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure Storage Linked Service** (Azure Storage társított szolgáltatás) elemet, és kattintson az **Add** (Hozzáadás) parancsra. 
   
    ![Új társított szolgáltatás](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Cserélje le az `<accountname>` és az `<accountkey>`* kifejezést az Azure Storage-tárfiókja nevére, illetve kulcsára. 
   
    ![Azure Storage társított szolgáltatás](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Mentse az **AzureStorageLinkedService1.json** fájlt.

> A JSON-tulajdonságokkal kapcsolatos információk: [Adatok áthelyezése Azure Blobból vagy Azure Blobba](data-factory-azure-blob-connector.md#azure-storage-linked-service).
> 
> 

### <a name="create-the-azure-sql-linked-service"></a>Az Azure SQL társított szolgáltatás létrehozása
1. A **Solution Explorerben** (Megoldáskezelőben) ismét kattintson a jobb gombbal a **Linked Services** (Társított szolgáltatások) csomópontra, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre. 
2. Ezúttal válassza a **Azure SQL Linked Service** (Azure SQL társított szolgáltatás) lehetőséget, és kattintson az **Add** (Hozzáadás) elemre. 
3. Az **AzureSqlLinkedService1.json fájlban** cserélje le a `<servername>`, `<databasename>`, `<username@servername>` és `<password>` paraméter értékét az Azure SQL-kiszolgáló, az adatbázis és a felhasználói fiók nevére, valamint a felhasználói fiók jelszavára.    
4. Mentse az **AzureSqlLinkedService1.json** fájlt. 

> [!NOTE]
> A JSON-tulajdonságokkal kapcsolatos információk: [Adatok áthelyezése SQL Database-ből vagy SQL Database-be](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
> 
> 

## <a name="create-datasets"></a>Adatkészletek létrehozása
Az előző lépésben létrehozta az **AzureStorageLinkedService1** és az **AzureSqlLinkedService1** társított szolgáltatásokat egy Azure Storage-fiók és egy Azure SQL Database az **ADFTutorialDataFactoryPSH** data factoryhoz való társításához. Ebben a lépésben két adatkészletet határoz meg – az **InputDataset** és az **OutputDataset** adatkészletet –, amelyek az AzureStorageLinkedService1 és az AzureSqlLinkedService1 szolgáltatás által hivatkozott adattárakban tárolt bemeneti/kimeneti adatokat jelölik. Az InputDataset adatkészlethez megadja a forrásadatokkal rendelkező blobot tároló blobtárolót. Az OutputDataset adatkészlethez megadja a kimeneti adatokat tároló SQL-táblát.

### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
Ebben a lépésben létrehozza az **InputDataset** nevű adatkészletet, amely egy, az Azure Storage-ben található blobtárolóra mutat, amelyet az **AzureStorageLinkedService1** társított szolgáltatás képvisel. A tábla egy téglalap alakú adatkészlet, és jelenleg ez az egyetlen támogatott adatkészlettípus. 

1. A **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal a **Tables** (Táblák) elemre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) elemre.
2. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure Blob** elemet, és kattintson az **Add** (Hozzáadás) elemre.   
3. Cserélje a JSON-szöveget az alábbi szövegre, és mentse az **AzureBlobLocation1.json** fájlt. 
   
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
           "linkedServiceName": "AzureStorageLinkedService1",
           "typeProperties": {
             "folderPath": "adftutorial/",
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
   
   * Az adatkészlet **típusa** **AzureBlob** értékre van állítva.
   * A **linkedServiceName** tulajdonság **AzureStorageLinkedService** értékre van állítva. Ezt a társított szolgáltatást a 2. lépésben hozta létre.
   * A **folderPath** (mappaútvonal) tulajdonság az **adftutorial** tárolóra van állítva. A blob nevét is megadhatja a mappán belül a **fileName** tulajdonsággal. Mivel itt nincs megadva a blob neve, a tárolóban lévő összes blob adata bemeneti adatnak minősül.  
   * A formátum **type** (típus) tulajdonsága **TextFormat** (Szövegformátum) értékre van állítva.
   * A szövegfájlban két mező található – a **FirstName** (Utónév) és a **LastName** (Vezetéknév) –, amelyeket egy vessző karakter választ el (**columnDelimiter**).    
   * Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre). Így a Data Factory szolgáltatás óránként keres bemeneti adatokat a megadott blobtároló (**adftutorial**) gyökérmappájában. 
   
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

> [!NOTE]
> A JSON-tulajdonságokkal kapcsolatos információk: [Adatok áthelyezése Azure Blobból vagy Azure Blobba](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
> 
> 

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása
Ebben a lépésben egy kimeneti adatkészletet hoz létre **OutputDataset** néven. Ez az adatkészlet egy SQL-táblára mutat abban az Azure SQL Database-ben, amelyet az **AzureSqlLinkedService1** jelöl. 

1. A **Solution Explorerben** (Megoldáskezelőben) ismét kattintson a jobb gombbal a **Tables** (Táblák) elemre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre.
2. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure SQL** elemet, és kattintson az **Add** (Hozzáadás) elemre. 
3. Cserélje a JSON-szöveget az alábbi JSON-szövegre, és mentse az **AzureSqlTableLocation1.json** fájlt.
   
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
           "linkedServiceName": "AzureSqlLinkedService1",
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
   
   * Az adatkészlet **type** (típus) tulajdonsága **AzureSQLTable** értékre van állítva.
   * A **linkedServiceName** tulajdonság az **AzureSqlLinkedService** értékre van állítva (ezt a társított szolgáltatást a 2. lépésben hozta létre).
   * A **tablename** tulajdonság **emp** értékre van állítva.
   * Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName**. Az ID azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.
   * Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre).  A Data Factory szolgáltatás óránként előállít egy kimeneti adatszeletet az Azure SQL Database **emp** táblájában.

> [!NOTE]
> A JSON-tulajdonságokkal kapcsolatos információk: [Adatok áthelyezése SQL Database-ből vagy SQL Database-be](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
> 
> 

## <a name="create-pipeline"></a>Folyamat létrehozása
Eddig bemeneti/kimeneti társított szolgáltatásokat és táblákat hozott létre. Most egy **másolási tevékenységgel** rendelkező folyamatot fog létrehozni, amely az Azure-blobból másol adatokat egy Azure SQL Database-adatbázisba. 

1. A **Megoldáskezelőben** kattintson a jobb gombbal a **Pipelines** (Folyamatok) elemre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre.  
2. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza a **Copy Data Pipeline** (Adatok másolása folyamat) elemet, és kattintson az **Add** (Hozzáadás) parancsra. 
3. Cserélje a JSON-t az alábbi JSON-ra, és mentse a **CopyActivity1.json** fájlt.
   
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
                 "style": "StartOfInterval",
                 "retry": 0,
                 "timeout": "01:00:00"
               }
             }
           ],
           "start": "2015-07-12T00:00:00Z",
           "end": "2015-07-13T00:00:00Z",
           "isPaused": false
         }
       }
   
   Vegye figyelembe a következő szempontokat:
   
   * A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **Copy** értékre van beállítva.
   * A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**.
   * A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva.
   
   A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra. Azt is megteheti, hogy a dátum-időpont paraméternek csak a dátum részét adja meg, az időpont részét pedig kihagyja. Megadhatja például a „2016-02-03” értéket, amely a következőnek felel meg: „2016-02-03T00:00:00Z”
   
   Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2016-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. 
   
   Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz.
   
   Az előző példában 24 adatszelet van, mert a rendszer óránként létrehoz egy adatszeletet.

## <a name="publishdeploy-data-factory-entities"></a>Data Factory-entitások közzététele/üzembe helyezése
Ebben a lépésben a korábban létrehozott Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) teszi közzé. Emellett megadja az entitások tárolására szolgáló új data factory nevét.  

1. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a projektre, majd kattintson a **Publish** (Közzététel) parancsra. 
2. Ha megjelenik a **Sign in to your Microsoft account** (Bejelentkezés a Microsoft-fiókba) párbeszédpanel, adja meg az Azure-előfizetéssel rendelkező fiókja hitelesítő adatait, és kattintson a **sign in** (bejelentkezés) elemre.
3. A következő párbeszédpanelnek kell megjelennie:
   
   ![Publish (Közzététel) párbeszédpanel](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. A Data Factory konfigurálása lapon hajtsa végre a következő lépéseket: 
   
   1. Válassza a **Create New Data Factory** (Új data factory létrehozása) lehetőséget.
   2. A **Name** (Név) mezőbe írja be a következőt: **VSTutorialFactory**.  
      
      > [!IMPORTANT]
      > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a közzététel során hibaüzenetet kap a data factory nevéről, módosítsa a data factory nevét (például sajátnévVSTutorialFactory értékre), majd próbálkozzon újra a közzététellel. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.        
      > 
      > 
   3. A **Subscription** (Előfizetés) mezőben válassza ki az Azure-előfizetést.
      
      > [!IMPORTANT]
      > Ha egy előfizetést sem lát, ellenőrizze, hogy olyan fiókkal jelentkezett-e be, amely rendszergazdája vagy társadminisztrátora az előfizetésnek.  
      > 
      > 
   4. Válassza ki a data factoryhoz az **erőforráscsoportot**. 
   5. Válassza ki a data factoryhoz a **régiót**. A legördülő listában csak a Data Factory szolgáltatás által támogatott régiók jelennek meg.
   6. Kattintson a **Tovább** gombra a **Publish Items** (Elemek közzététele) oldalra való váltáshoz.
      
       ![Configure data factory (Data factory konfigurálása) oldal](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. A **Publish Items** (Elemek közzététele) oldalon győződjön meg arról, hogy az összes Data Factory-entitás ki van jelölve, és kattintson a **Tovább** gombra a **Summary** (Összegzés) oldalra való váltáshoz.
   
   ![Publish items (Elemek közzététele) oldal](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Tekintse át az összefoglalót, és kattintson a **Tovább** gombra az üzembehelyezési folyamat elindításához, és a **Deployment Status** (Üzembehelyezési állapot) megtekintéséhez.
   
   ![Publish summary (Összefoglaló közzététele) oldal](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. A **Deployment Status** (Üzembehelyezési állapot) oldalon meg kell jelennie az üzembehelyezési folyamat állapotának. Miután befejeződött az üzembe helyezés, kattintson a Finish (Befejezés) gombra. 
   ![Deployment status page (Üzembehelyezési állapot oldal)](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png) Vegye figyelembe a következő szempontokat: 

* Ha a „**This subscription is not registered to use namespace Microsoft.DataFactory**” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel: 
  
  * Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához. 
    
          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
      Az alábbi parancs futtatásával ellenőrizheti, hogy a Data Factory-szolgáltató regisztrálva van-e. 
    
          Get-AzureRmResourceProvider
  * Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel a művelettel automatikusan regisztrálja a szolgáltatót.
* A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.

> [!IMPORTANT]
> Data Factory-példányok létrehozásához az Azure-előfizetés adminisztrátorának/társadminisztrátorának kell lennie
> 
> 

## <a name="summary"></a>Összefoglalás
Az oktatóanyag során létrehozott egy Azure data factoryt, hogy adatokat másoljon egy Azure-blobból egy Azure SQL Database-adatbázisba. A Visual Studiót használta a data factory, a társított szolgáltatások, az adatkészletek és a folyamat létrehozásához. Az oktatóanyag során a következő főbb lépéseket végezte el:  

1. Létrehozott egy Azure **data factoryt**.
2. **Társított szolgáltatásokat** hozott létre:
   1. Egy **Azure Storage** társított szolgáltatást a bemeneti adatokat tároló Azure Storage-fiók társításához.     
   2. Egy **Azure SQL** társított szolgáltatást a kimeneti adatokat tároló Azure SQL Database társításához. 
3. **Adatkészleteket** hozott létre, amelyek az adatcsatorna bemeneti és kimeneti adatait írják le.
4. Létrehozott egy **folyamatot** egy **Másolási tevékenységgel**, ahol a **BlobSource** a forrás, az **SqlSink** pedig a fogadó. 

## <a name="use-server-explorer-to-view-data-factories"></a>A Kiszolgókezelő használata data factoryk megtekintéséhez
1. A **Visual Studio** menüjében kattintson a **View** (Megtekintés), majd a **Server Explorer** (Kiszolgálókezelő) elemre.
2. A Server Explorer (Kiszolgálókezelő) ablakban bontsa ki az **Azure**, majd a **Data Factory** elemet. Ha megjelenik a **Sign in to Visual Studio** (Jelentkezzen be a Visual Studióba) üzenet, adja meg az Azure-előfizetéséhez társított **fiókot**, és kattintson a **Continue** (Folytatás) elemre. Adja meg a **jelszót**, és kattintson a **Sign in** (Bejelentkezés) elemre. A Visual Studio megpróbálja lekérni az információkat az előfizetésében elérhető összes Azure data factoryről. Ennek a műveletnek az állapota a **Data Factory Task List** (Data Factory-feladatlista) ablakban jelenik meg.
    ![Server Explorer (Kiszolgálókezelő)](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Ha egy meglévő data factory alapján szeretne létrehozni Visual Studio-projektet, kattintson a jobb gombbal egy dara factoryre, és válassza az Export Data Factory to New Project (Data factory exportálása új projektbe) lehetőséget.
    ![Data factory exportálása VS-projektbe](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Visual Studióhoz készült Data Factory-eszközök frissítése
A Visual Studióhoz készült Data Factory-eszközök frissítéséhez tegye a következőket:

1. Kattintson a menüben a **Tools** (Eszközök) elemre, és válassza az **Extensions and Updates** (Bővítmények és frissítések) lehetőséget. 
2. A bal oldali panelen válassza az **Updates** (Frissítések) elemet, majd válassza a **Visual Studio Gallery** (Visual Studio-gyűjtemény) lehetőséget.
3. Válassza az **Azure Data Factory tools for Visual Studio** lehetőséget, és kattintson az **Update** (Frissítés) elemre. Ha nem látja ezt a bejegyzést, már rendelkezik az eszközök legújabb verziójával. 

A [Monitor datasets and pipeline](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) (Adatkészletek és folyamatok figyelése) című cikkben útmutatást találhat arról, hogy hogyan használhatja az Azure Portalt az oktatóanyagban létrehozott folyamatok és adatkészletek figyeléséhez.

## <a name="see-also"></a>Lásd még:
| Témakör | Leírás |
|:--- |:--- |
| [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) |Ez a cikk részletes információkat tartalmaz a jelen oktatóanyagban használt Másolás tevékenységről. |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) |Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok](data-factory-create-pipelines.md) |Ennek a cikknek a segítségével megismerheti a folyamatokat és tevékenységeket az Azure Data Factoryban. |
| [Adatkészletek](data-factory-create-datasets.md) |Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban. |
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) |Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. |




<!--HONumber=Nov16_HO2-->


