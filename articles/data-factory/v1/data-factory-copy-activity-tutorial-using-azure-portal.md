---
title: 'Oktatóanyag: Azure Data Factory-folyamatok létrehozása adatmásolás céljából (Azure Portal) | Microsoft Docs'
description: Az oktatóanyagban az Azure Portal használatával hoz létre egy másolási tevékenységgel rendelkező Azure Data Factory-folyamatot az adatok Azure Blob Storage-ból Azure SQL Database-be való másolásához.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 09cad6b76b2fdefe1163530e5437427faf29d72f
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046867"
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>Oktatóanyag: Az Azure Portal használata Data Factory-folyamatok létrehozására adatmásolás céljából 
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

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [másolási tevékenység oktatóanyagát](../quickstart-create-data-factory-dot-net.md). 

A cikk útmutatást nyújt adat-előállítók [Azure Portal](https://portal.azure.com) használatával való létrehozására olyan folyamatokkal, amelyek az Azure Blob Storage-ból másolnak adatokat az Azure SQL Database-be. Ha még csak ismerkedik az Azure Data Factory szolgáltatással, olvassa el a [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md) című cikket az oktatóanyag elvégzése előtt.   

Az oktatóanyag segítségével egyetlen tevékenységgel (másolási tevékenységgel) rendelkező folyamatot hozhat létre. A másolási tevékenység adatokat másol a forrásadattárból egy támogatott fogadó adattárba. A forrásként és fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. További információ a másolási tevékenységről: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md).

Egy folyamathoz több tevékenység is tartozhat. Ezenkívül össze is fűzhet két tevékenységet (egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. További információért lásd: [egy folyamaton belüli több tevékenység](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Az oktatóanyagban található adatfeldolgozási folyamat adatokat másol egy forrásadattárból egy céladattárba. Az adatok Azure Data Factory használatával történő átalakításának útmutatásáért olvassa el [az adatok Hadoop-fürt segítségével történő átalakítására szolgáló folyamat létrehozását ismertető oktatóanyagot](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzése előtt hajtsa végre [Az oktatóanyag előfeltételei](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikkben előfeltételként felsorolt lépéseket.

## <a name="steps"></a>Lépések
Az oktatóanyag során a következő lépéseket fogja elvégezni:

1. Hozzon létre egy folyamatot az **adat-előállítóban**. Ebben a lépésben egy adat-előállítót hoz létre ADFTutorialDataFactory néven. 
2. Hozzon létre **társított szolgáltatásokat** az adat-előállítóban. Ebben a lépésben a következő két típusú társított szolgáltatást hozza létre: Azure Storage és Azure SQL Database. 
    
    Az AzureStorageLinkedService az Azure Storage-fiókot társítja az adat-előállítóval. Létrehozott egy tárolót, és adatokat töltött fel ebbe a tárfiókba az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként.   

    Az AzureSqlLinkedService az Azure SQL Database-t társítja az adat-előállítóval. A blobtárolóból másolt adatokat a rendszer ebben az adatbázisban tárolja. Az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként létrehozta az SQL-táblát az adatbázisban.   
3. Hozza létre a bemeneti és kimeneti **adatkészleteket** az adat-előállítóban.  
    
    Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. A bemeneti blob adatkészlete pedig a tárolót és a bemeneti adatokat tartalmazó mappát határozza meg.  

    Ehhez hasonlóan az Azure SQL Database társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure SQL Database csatlakoztatásához használ. Az SQL-tábla kimeneti adatkészlete határozza meg azt az adatbázistáblát, amelybe a rendszer a blobtárolóból származó adatokat másolja.
4. Hozzon létre egy **folyamatot** az adat-előállítóban. Ebben a lépésben létre fog hozni egy másolási tevékenységgel rendelkező folyamatot.   
    
    A másolási tevékenység adatokat másol az Azure Blob Storage-ból az Azure SQL Database egyik táblájába. A folyamat másolási tevékenységével adatokat másolhat bármely támogatott forrásból bármely támogatott célhelyre. A támogatott adattárak listájáért lásd: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
5. Monitorozza a folyamatot. Ebben a lépésben a bemeneti és a kimeneti adatkészletek szeleteit **monitorozhatja** az Azure Portal használatával. 

## <a name="create-data-factory"></a>Data factory létrehozása
> [!IMPORTANT]
> Hajtsa végre az [oktatóanyag előfeltételi lépéseit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), ha még nem tette volna meg.   

A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Például egy olyan másolási tevékenység, amely adatokat másol egy forrásadattárból egy céladattárba, és egy HDInsight Hive-tevékenység, amely egy Hive-szkript futtatásával alakítja át a bemeneti adatokat kimeneti adatokká. Ebben a lépésben létrehozzuk a data factoryt.

1. Miután bejelentkezett az [Azure Portalra](https://portal.azure.com/), kattintson a bal oldali menüben az **Erőforrás létrehozása** elemre, és válassza az **Adatok + analitika** lehetőséget, majd kattintson a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. A **New data factory** (Új data factory) panelen:
   
   1. A**Name** (Név) mezőbe írja be a következőt: **ADFTutorialDataFactory**. 
      
         ![A New data factory (Új data factory) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a data factory nevét (például sajátnévADFTutorialDataFactory-ra), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![A Data Factory name not available (A data factory neve nem érhető el) üzenet](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
   3. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
      
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
          Az oktatóanyag egyes lépései azt feltételezik, hogy az **ADFTutorialResourceGroup** nevet adta az erőforráscsoportnak. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
   4. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory szolgáltatás által támogatott régiók jelennek meg.
   5. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
   6. Kattintson a **Create** (Létrehozás) gombra.
      
      > [!IMPORTANT]
      > Data Factory-példány létrehozásához a [Data Factory közreműködője](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.
      > 
      > A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.                
      > 
      > 
3. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. A létrehozás befejezése után a **Data Factory** panel a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben az oktatóanyagban nem használunk számítási szolgáltatásokat (például Azure HDInsight vagy Azure Data Lake Analytics). Csak kétféle típusú adattárat használunk: Azure Storage (forrás) és Azure SQL Database (cél). 

Ezért két társított szolgáltatást fog létrehozni AzureStorageLinkedService és AzureSqlLinkedService néven (típus: AzureStorage és AzureSqlDatabase).  

Az AzureStorageLinkedService az Azure Storage-fiókot társítja az adat-előállítóval. Ebben a tárfiókban hozta létre a tárolót, és ebbe töltötte fel az adatokat az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként.   

Az AzureSqlLinkedService az Azure SQL Database-t társítja az adat-előállítóval. A blobtárolóból másolt adatokat a rendszer ebben az adatbázisban tárolja. Az [előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) részeként létrehozta az emp táblát az adatbázisban.  

### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
Ebben a lépésben társítja az Azure Storage-fiókot az adat-előállítóval. Ebben a szakaszban megadhatja az Azure-tárfiók nevét és kulcsát.  

1. A **Data Factory** (Adat-előállító) panelen kattintson az **Author and deploy** (Fejlesztés és üzembe helyezés) csempére.
   
   ![Az Author and deploy (Fejlesztés és üzembe helyezés) csempe](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. Megjelenik a **Data Factory szerkesztője** az alábbi ábrán látható módon: 

    ![A Data Factory szerkesztője](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. A szerkesztőben kattintson az eszköztár **New data store** (Új adattár) gombjára, és a legördülő menüben válassza az **Azure Storage** lehetőséget. A jobb oldali panelen megjelenik az Azure Storage társított szolgáltatás létrehozására szolgáló JSON-sablon. 
   
    ![A szerkesztő New data store (Új adattár) gombja](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Az `<accountname>` és az `<accountkey>` kifejezéseket cserélje az Azure-tárfiókja nevére, illetve kulcsára. 
   
    ![Szerkesztő – A blobtároló JSON-fájlja](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Kattintson az eszköztár **Üzembe helyezés** gombjára. Az üzembe helyezett **AzureStorageLinkedService** szolgáltatásnak meg kell jelennie a fanézetben. 
   
    ![Szerkesztő – A blobtároló üzembe helyezése](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    További információ a társított szolgáltatás definíciójában található JSON-tulajdonságokról: [Azure Blob Storage-összekötő](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Társított szolgáltatás létrehozása az Azure SQL Database-hez
Ebben a lépésben társítani fogja az Azure SQL-adatbázist az adat-előállítóhoz. Ebben a szakaszban megadhatja az Azure SQL-kiszolgáló nevét, az adatbázis nevét, a felhasználónevet és a felhasználói jelszót. 

1. A **Data Factory Editorban** kattintson az eszköztár **Új adattár** gombjára, és a legördülő menüben válassza az **Azure SQL Database** lehetőséget. A jobb oldali panelen megjelenik az Azure SQL társított szolgáltatás létrehozására szolgáló JSON-sablon.
2. Cserélje le a `<servername>`, `<databasename>`, `<username>@<servername>` és `<password>` paraméter értékét az Azure SQL-kiszolgáló, az adatbázis és a felhasználói fiók nevére, valamint a felhasználói fiók jelszavára. 
3. Kattintson az eszköztár **Üzembe helyezés** gombjára az **AzureSqlLinkedService** létrehozásához és üzembe helyezéséhez.
4. Győződjön meg arról, hogy az **AzureSqlLinkedService** szolgáltatás megjelenik a **Társított szolgáltatások** faszerkezetes nézetében.  

    További információ a JSON-tulajdonságokról: [Azure SQL Database-összekötő](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Adatkészletek létrehozása
Az előző lépésben létrehozta az Azure Storage-fiók és az Azure SQL Database összekapcsolását végző társított szolgáltatásokat. Ebben a lépésben két adatkészletet határoz meg – InputDataset és OutputDataset néven –, amelyek az AzureStorageLinkedService és az AzureSqlLinkedService szolgáltatás által hivatkozott bemeneti és kimeneti adatokat jelölik.

Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. A bemeneti blob adatkészlete (InputDataset) pedig a tárolót és a bemeneti adatokat tartalmazó mappát határozza meg.  

Ehhez hasonlóan az Azure SQL Database társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure SQL Database csatlakoztatásához használ. Az SQL-tábla kimeneti adatkészlete (OututDataset) határozza meg azt az adatbázistáblát, amelybe a rendszer a blobtárolóból származó adatokat másolja. 

### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
Ebben a lépésben hozza létre az InputDataset nevű adatkészletet, amely az AzureStorageLinkedService társított szolgáltatás által hivatkozott Azure Storage blobtárolójának (adftutorial) gyökérmappájában található blobfájlra mutat (emp.txt). Ha nem ad meg értéket a fájlnévnek (vagy kihagyja azt), a rendszer a bemeneti mappában található összes blob adatát a célhelyre másolja. Ebben az oktatóanyagban a fileName értékét adja meg. 

1. A Data Factory **szerkesztőjében** kattintson a **... Továbbiak**, majd az **Új adatkészlet**, végül az **Azure Blob Storage** elemre a legördülő menüben. 
   
    ![Új adatkészlet menü](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. A jobb oldali panelen cserélje le a JSON-t a következő JSON-kódrészletre: 
   
    ```json
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
    ```   

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

    | Tulajdonság | Leírás |
    |:--- |:--- |
    | type | A tulajdonság beállításának értéke **AzureBlob**, mert az adatok egy Azure Blob Storage-tárban találhatók. |
    | linkedServiceName | A korábban létrehozott **AzureStorageLinkedService** szolgáltatásra hivatkozik. |
    | folderPath | A **blobtárolót** és a bemeneti blobokat tartalmazó **mappát** határozza meg. Ebben az oktatóanyagban az adftutorial a blobtároló és a folder a gyökérmappa. | 
    | fileName | Ez a tulajdonság nem kötelező. Ha kihagyja, a rendszer a folderPath elérési úton található összes fájlt kiválasztja. Ebben az oktatóanyagban az **emp.txt** a fileName értéke, így a rendszer csak ezt a fájlt használja a feldolgozáshoz. |
    | formátum -> típus |A bemeneti fájl szöveges formátumú, ezért a **TextFormat** értéket használjuk. |
    | columnDelimiter | A bemeneti fájlban **vesszővel (`,`)** vannak elválasztva az oszlopok. |
    | frequency/interval | A frequency (gyakoriság) beállítása **Hour** (Óra), az interval (időköz) beállítása pedig **1**, ami azt jelenti, hogy a bemeneti szeletek **óránként** érhetők el. Vagyis a Data Factory szolgáltatás óránként keres bemeneti adatokat a megadott blobtároló (**adftutorial**) gyökérmappájában. A szolgáltatás a folyamat kezdő és befejező időpontja közti időszakban – és nem azon kívül – keres adatokat.  |
    | external | Ez a tulajdonság a **true** (igaz) értékre van állítva, ha az adatokat nem ez a folyamat hozta létre. Az oktatóanyagban használt bemeneti adatok az emp.txt fájlban találhatók, amelyet nem ez a folyamat hoz létre, ezért ezt a tulajdonságot true (igaz) értékre állítottuk. |

    Ezekről a JSON-tulajdonságokról további tudnivalók az [Azure Blob-összekötőről](data-factory-azure-blob-connector.md#dataset-properties) szóló cikkben olvashatók.      
3. Kattintson az eszköztár **Üzembe helyezés** gombjára az **InputDataset** adatkészlet létrehozásához és üzembe helyezéséhez. Győződjön meg arról, hogy az **InputDataset** adatkészlet megjelenik a fanézetben.

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása
Az Azure SQL Database társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure SQL Database csatlakoztatásához használ. Az ebben a lépésben létrehozandó SQL-tábla kimeneti adatkészlete (OututDataset) határozza meg azt az adatbázistáblát, amelybe a rendszer a blobtárolóból származó adatokat másolja.

1. A Data Factory **szerkesztőjében** kattintson a **... Továbbiak**, majd az **Új adatkészlet**, végül az **Azure SQL** elemre a legördülő menüben. 
2. A jobb oldali panelen cserélje le a JSON-t a következő JSON-kódrészletre:

    ```json   
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
    ```     

    Az alábbi táblázat ismerteti a kódrészletben használt JSON-tulajdonságokat:

    | Tulajdonság | Leírás |
    |:--- |:--- |
    | type | A type tulajdonság beállítása **AzureSqlTable**, mert az adatok másolása az Azure SQL Database egyik táblájába történik. |
    | linkedServiceName | A korábban létrehozott **AzureSqlLinkedService** szolgáltatásra hivatkozik. |
    | tableName | Azt a **táblát** határozza meg, amelybe a rendszer az adatokat másolja. | 
    | frequency/interval | A frequency (gyakoriság) értéke **Hour** (Óra), az interval (időköz) értéke pedig **1**, azaz a rendszer a kimeneti szeleteket **óránként** állítja elő a folyamat kezdő és befejező időpontja közti időszakban (és nem azon kívül).  |

    Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName**. Az ID azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.

    További információ ezekről a JSON-tulajdonságokról: [Azure SQL-összekötő](data-factory-azure-sql-connector.md#dataset-properties).
3. Kattintson az eszköztár **Üzembe helyezés** gombjára az **OutputDataset** adatkészlet létrehozásához és üzembe helyezéséhez. Győződjön meg arról, hogy az **OutputDataset** megjelenik az **Adatkészletek** faszerkezetes nézetében. 

## <a name="create-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy **másolási tevékenységgel** rendelkező folyamatot, amely bemenetként az **InputDataset**, kimenetként pedig az **OutputDataset** adatkészletet használja.

Jelenleg a kimeneti adatkészlet határozza meg az ütemezést. Az oktatóanyagban a kimeneti adatkészletet úgy konfiguráljuk, hogy a szeletek létrehozása óránként történjen meg. A folyamat kezdő és befejező időpontja között egy nap, azaz 24 óra telik el. Ezért a folyamat a kimeneti adatkészletből 24 szeletet hoz létre. 

1. A Data Factory **szerkesztőjében** kattintson a **... Továbbiak**, majd az **Új adatcsatorna** elemre. Azt is megteheti, hogy a jobb gombbal a **Pipelines** (Folyamatok) elemre kattint a fanézetben, majd a **New pipeline** (Új folyamat) parancsot választja.
2. A jobb oldali panelen cserélje le a JSON-t a következő JSON-kódrészletre: 

    ```json   
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
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    Vegye figyelembe a következő szempontokat:
   
    - A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **Copy** értékre van beállítva. További információ a másolási tevékenységről: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md). A Data Factory megoldásaiban használhatja az [adatátalakítási tevékenységeket](data-factory-data-transformation-activities.md) is.
    - A tevékenység bemenetének beállítása **InputDataset**, a kimeneté pedig **OutputDataset**. 
    - A **typeProperties** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva. A másolási tevékenység által forrásként és fogadóként támogatott adattárak teljes listájáért lásd: [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Egy forrásként/fogadóként támogatott konkrét adattár használatával kapcsolatos útmutatóért kattintson a tábla adott hivatkozására.
    - Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2016-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz.
     
    Az előző példában 24 adatszelet van, mert a rendszer óránként létrehoz egy adatszeletet.

    A folyamathoz tartozó definíció JSON-tulajdonságainak leírásáért lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). A másolási tevékenységhez tartozó definíció JSON-tulajdonságainak leírásáért lásd: [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md). A BlobSource által támogatott JSON-tulajdonságok leírásáért lásd: [Azure Blob-összekötő](data-factory-azure-blob-connector.md). Az SqlSink által támogatott JSON-tulajdonságok leírásáért lásd: [Azure SQL Database-összekötő](data-factory-azure-sql-connector.md).
3. Kattintson az eszköztár **Deploy** (Üzembe helyezés) gombjára az **ADFTutorialPipeline** létrehozásához és üzembe helyezéséhez. Győződjön meg arról, hogy a folyamat megjelenik a fanézetben. 
4. Most pedig az **X**-re kattintva zárja be az **Editor** (Szerkesztő) panelt. Kattintson újra az **X-re** az **ADFTutorialDataFactory** **Data Factory**-kezdőlapjának megjelenítéséhez.

**Gratulálunk!** Sikeresen létrehozott egy Azure-beli adat-előállítót egy olyan folyamattal, amely az Azure Blob Storage-ból az Azure SQL Database-be másol adatokat. 


## <a name="monitor-pipeline"></a>Folyamat figyelése
Ebben a lépésben az Azure Portal használatával figyeli egy Azure data factory eseményeit.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Folyamat figyelése a Monitor & Manage alkalmazással
A következő lépések útmutatót nyújtanak az adat-előállító folyamatainak monitorozására a Monitor & Manage alkalmazás használatával: 

1. Kattintson a **Monitor & Manage** csempére a Data Factory kezdőlapján.
   
    ![Monitor & Manage csempe](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. A **Monitor & Manage alkalmazásnak** egy külön lapon kell megjelennie. 

    > [!NOTE]
    > Ha a böngésző az „Authorizing...” (Hitelesítés) lépésnél elakad, törölje a **Block third party cookies and site data** (Harmadik féltől származó cookie-k és webhelyadatok tiltása) jelölőnégyzet bejelölését, (vagy) hozzon létre kivételt a **login.microsoftonline.com** webhelyhez, majd próbálkozzon ismét az alkalmazás megnyitásával.

    ![Monitor & Manage alkalmazás](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. Módosítsa a **kezdési idő** és a **befejezési idő** értékét úgy, hogy az megfeleljen a folyamat kezdési (2017-05-11) és befejezési (2017-05-12) időpontjának, majd kattintson az **Alkalmaz** gombra.       
3. A középső panel listájában megjelennek a folyamat kezdési és befejezési időpontja közötti órákhoz tartozó **tevékenységablakok**. 
4. Válasszon egy tevékenységablakot a **Tevékenységablakok** listában a részletek megtekintéséhez. 
    ![Tevékenységablakok részletei](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    A jobb oldali Activity Window Explorer mutatja, hogy az aktuális UTC időnek (20:12) megfelelő összes szelet feldolgozása megtörtént (zöld szín). A 20:00-21:00, a 21:00-22:00, a 22:00-23:00 és a 23:00-00:00 órának megfelelő szeletek feldolgozása még nem történt meg.

    A jobb oldali panelen látható **Kísérletek** szakasz mutatja az adatszelethez tartozó tevékenységfuttatási adatokat. Hiba esetén a hibarészletek is a panelen láthatók. Például ha a bemeneti mappa vagy a tároló nem létezik, és a folyamat feldolgozása nem sikerül, a rendszer hibaüzenetben tájékoztat arról, hogy a tároló vagy a mappa nem található.

    ![Tevékenységfuttatási kísérletek](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. Indítsa el az **SQL Server Management Studiót**, csatlakozzon az Azure SQL Database-hez, és ellenőrizze, hogy a sorok megjelennek-e az adatbázis **emp** táblájában.
    
    ![SQL-lekérdezés eredményei](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

Az alkalmazás használatával kapcsolatos részletes információkért olvassa el a [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App](data-factory-monitor-manage-app.md) (Azure Data Factory-folyamatok figyelése és felügyelete a Monitoring and Management használatával) című cikket.

### <a name="monitor-pipeline-using-diagram-view"></a>Folyamat figyelése diagramnézetben
Az adatfolyamatok monitorozását a diagramnézet használatával is végezheti.  

1. A **Data Factory** panelen kattintson a **Diagram** elemre.
   
    ![Data Factory panel – Diagram csempe](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Az alábbi képhez hasonló diagramnak kell megjelennie: 
   
    ![Diagramnézet](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. A diagramnézetben az **InputDataset** elemre duplán kattintva tekintheti meg az adatkészlet szeleteit.  
   
    ![Adatkészletek kiválasztott InputDataset elemmel](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Az összes adatszelet megjelenítéséhez kattintson a **Részletek** hivatkozásra. Megjelennek a 24 órához tartozó szeletek a folyamat kezdési és befejezési időpontja között. 
   
    ![Összes bemeneti adatszelet](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Figyelje meg, hogy az aktuális UTC időpontig már létrejöttek az adatszeletek, és **Kész** állapotúak, mert az **emp.txt** fájl folyamatosan megtalálható a következő blobtárolóban: **adftutorial\input**. A jövőbeli időpontokhoz tartozó szeletek még nem Kész állapotúak. Győződjön meg arról, hogy a panel alsó részén található **Recently failed slices** (Legutóbbi sikertelen szeletek) részben egy szelet sem jelenik meg.
6. Zárja be a paneleket, amíg meg nem jelenik a diagramnézet, (vagy) balra görgetve jelenítheti meg a diagramnézetet. Ezután kattintson duplán az **OutputDataset** elemre. 
8. Az összes szeletet az **OutputDataset** **Tábla** paneljének **Részletek** hivatkozására kattintva tekintheti meg.

    ![Data slices (Adatszeletek) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. Figyelje meg, hogy az aktuális UTC időpontig az összes szelet állapota a következők szerint módosul: **Végrehajtásra vár** => **Folyamatban van** ==> **Kész**. A múltbeli időpontokhoz tartozó (a jelenlegi időpontnál korábbi) szeletek feldolgozása alapértelmezés szerint a legújabb elemtől a legrégebbi elemig történik. Például ha a jelenlegi idő 20:12, a 19:00-20:00 óra szeletének feldolgozása a 18:00-19:00 órához tartozó szelet feldolgozása előtt történik. A 20:00-21:00 órához tartozó szelet feldolgozása alapértelmezés szerint az időintervallum végén, azaz 21:00 után történik.  
10. Kattintson a listában lévő bármelyik adatszeletre, és megjelenik az **Adatszelet** panel. A tevékenységablakokhoz tartozó adatszeleteket nevezzük szeletnek. Egy szelet lehet egy vagy több fájl.  
    
     ![Data slice (Adatszelet) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Ha a szelet nem **Ready** (Kész) állapotú, az **Upstream slices that are not ready** (Nem kész állapotú fölérendelt szeletek) listában láthatja azokat a nem kész állapotú fölérendelt szeleteket, amelyek blokkolják az aktuális szelet végrehajtását.
11. A **DATA SLICE** (ADATSZELET) panel alsó részén található listában az összes tevékenységfuttatás megjelenik. Kattintson egy **tevékenységfuttatásra** az **Tevékenységfuttatás részletei** panel megjelenítéséhez. 
    
    ![Az Activity Run Details (Tevékenységfuttatás részletei) panel](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    Ezen a panelen tekintheti meg, hogy mennyi ideig tart a másolási művelet, mekkora az átviteli sebesség, a rendszer hány bájt adatot olvasott és írt, mikor van a futtatás kezdete és vége, stb.  
12. Kattintson az **X**-re az összes panel bezárásához, amíg vissza nem tér az **ADFTutorialDataFactory** kezdőpaneljére.
13. (nem kötelező) Az előző lépésekben látott paneleket az **Adatkészletek** vagy a **Folyamatok** csempére kattintva érheti el. 
14. Indítsa el az **SQL Server Management Studiót**, csatlakozzon az Azure SQL Database-hez, és ellenőrizze, hogy a sorok megjelennek-e az adatbázis **emp** táblájában.
    
    ![SQL-lekérdezés eredményei](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>Összegzés
Az oktatóanyag során létrehozott egy Azure data factoryt, hogy adatokat másoljon egy Azure-blobból egy Azure SQL Database-adatbázisba. Az Azure Portalt használta a data factory, a társított szolgáltatások, az adatkészletek és a folyamat létrehozásához. Az oktatóanyag során a következő főbb lépéseket végezte el:  

1. Létrehozott egy Azure **data factoryt**.
2. **Társított szolgáltatásokat** hozott létre:
   1. Egy **Azure Storage** társított szolgáltatást a bemeneti adatokat tároló Azure Storage-fiók társításához.     
   2. Egy **Azure SQL** társított szolgáltatást a kimeneti adatokat tároló Azure SQL Database társításához. 
3. **Adatkészleteket** hozott létre, amelyek a folyamat bemeneti és kimeneti adatait írják le.
4. Létrehozott egy **folyamatot** egy **Másolási tevékenységgel**, ahol a **BlobSource** a forrás, az **SqlSink** pedig a fogadó.  

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag egy olyan másolási műveletet mutatott be, amelynek a forrásadattára egy Azure Blob Storage-tár, a céladattára pedig egy Azure SQL-adatbázis volt. Az alábbi táblázatban a másolási tevékenység által támogatott forrásadattárak és céladattárak listája látható: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

A táblázatban lévő adattárak hivatkozására kattintva megismerheti az adattárakba és az adattárakból történő adatmásolás módszereit.