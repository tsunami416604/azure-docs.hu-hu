<properties 
    pageTitle="Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Visual Studio használatával" 
    description="Ez az oktatóanyag bemutatja, hogyan hozhat létre másolási tevékenységgel rendelkező Azure Data Factory-folyamatot a Visual Studio használatával." 
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

# Oktatóanyag: Másolási tevékenységgel rendelkező folyamat létrehozása a Visual Studio használatával
> [AZURE.SELECTOR]
- [Az oktatóanyag áttekintése](data-factory-get-started.md)
- [A Data Factory Editor használata](data-factory-get-started-using-editor.md)
- [A Visual Studio használata](data-factory-get-started-using-vs.md)
- [A PowerShell használata](data-factory-monitor-manage-using-powershell.md)
- [A Másolás varázsló használata](data-factory-copy-data-wizard-tutorial.md)

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani a Visual Studio 2013 használatával:

1. Létrehozza a következő két társított szolgáltatást: **AzureStorageLinkedService1** és **AzureSqlinkedService1**. Az AzureStorageLinkedService1 egy Azure Storage-ot, míg az AzureSqlLinkedService1 egy Azure SQL Database-t társít az **ADFTutorialDataFactoryVS** data factoryhoz. A folyamat bemeneti adatai az Azure Blob Storage egyik blobtárolójában, a kimeneti adatok pedig az Azure SQL Database egyik táblájában találhatók. Ezért ezt a két adattárat társított szolgáltatásként kell hozzáadnia a data factoryhez.
2. Létrehoz két data factory-táblát: az **EmpTableFromBlob** és az **EmpSQLTable** táblát, amelyek az adattárakban tárolt bemeneti/kimeneti adatokat képviselik. Az EmpTableFromBlob táblához meg fogja adni a forrásadatokkal rendelkező blobot tároló blobtárolót, az EmpSQLTable táblához pedig meg fogja adni a kimeneti adatokat tároló SQL-táblát. Egyéb tulajdonságokat is meg fog adni, például az adatok szerkezetét, rendelkezésre állását stb.
3. Létrehozza az **ADFTutorialPipeline** nevű folyamatot az ADFTutorialDataFactoryVS data factoryban. A folyamat része lesz egy **Másolási tevékenység**, amely bemeneti adatokat másol az Azure blobból a kimeneti Azure SQL-táblába. A Másolás tevékenység végzi az adattovábbítást az Azure Data Factoryben, és a tevékenységet egy olyan, globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és méretezhető módon másolja az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket. 
4. Létrehoz egy data factoryt, és társított szolgáltatásokat, táblákat, valamint egy folyamatot helyez üzembe.    

## Előfeltételek
Tekintse meg [Az oktatóanyag áttekintése](data-factory-get-started.md) című cikket, és az oktatóanyag elvégzése előtt hajtsa végre az előfeltételként felsorolt lépéseket.

A számítógépre a következőket kell telepíteni: 
- Visual Studio 2013
- Töltse le az Azure SDK-t a Visual Studio 2013-hoz. Nyissa meg az [Azure letöltési oldalát](https://azure.microsoft.com/downloads/), és kattintson a **VS 2013** elemre a **.NET** szakaszban.

## Visual Studio-projekt létrehozása 
1. Indítsa el a **Visual Studio 2013-at**. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre. Meg kell jelennie a **New project** (Új projekt) párbeszédpanelnek.  
2. A **New project** (Új projekt) párbeszédpanelen jelölje ki a **DataFactory** sablont, és kattintson az **Empty Data Factory Project** (Üres Data Factory-projekt) elemre. Ha nem látja a DataFactory sablont, zárja be a Visual Studiót, telepítse az Azure SDK-t a Visual Studio 2013-hoz, és nyissa meg újra a Visual Studiót.  

    ![A New project (Új projekt) párbeszédpanel](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Adja meg a projekt **nevét**, a **helyet**, valamint a **megoldás** nevét, és kattintson az **OK** gombra.

    ![Megoldáskezelő](./media/data-factory-get-started-using-vs/solution-explorer.png)   

## Társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Az adattárak lehetnek Azure Storage-tárolók, Azure SQL Database-adatbázisok vagy helyszíni SQL Server-adatbázisok.

Ebben a lépésben létrehozza a következő két társított szolgáltatást: **AzureStorageLinkedService1** és **AzureSqlLinkedService1**. Az AzureStorageLinkedService1 társított szolgáltatás egy Azure Storage-fiókot, az AzureSqlLinkedService pedig egy Azure SQL Database adatbázist társít az **ADFTutorialDataFactory** data factoryhoz. 

### Az Azure Storage társított szolgáltatás létrehozása

4. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **Linked Services** (Társított szolgáltatások) elemre, mutasson az **Add** (Hozzáadás) parancsra, és kattintson a **New Item** (Új elem) elemre.      
5. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure Storage Linked Service** (Azure Storage társított szolgáltatás) elemet, és kattintson az **Add** (Hozzáadás) parancsra. 

    ![Új társított szolgáltatás](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Az **accountname** és az **accountkey**kifejezéseket cserélje az Azure Storage-tárfiókja nevére, illetve kulcsára. 

    ![Azure Storage társított szolgáltatás](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Mentse az **AzureStorageLinkedService1.json** fájlt.

### Az Azure SQL társított szolgáltatás létrehozása

5. A **Solution Explorerben** (Megoldáskezelőben) ismét kattintson a jobb gombbal a **Linked Services** (Társított szolgáltatások) csomópontra, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre. 
6. Ezúttal válassza a **Azure SQL Linked Service** (Azure SQL társított szolgáltatás) lehetőséget, és kattintson az **Add** (Hozzáadás) elemre. 
7. Az **AzureSqlLinkedService1.json fájlban** cserélje le a **servername**, **databasename**, **username@servername** és **password** paraméterek értékét az Azure SQL-kiszolgáló, az adatbázis és a felhasználói fiók nevére, valamint a felhasználói fiók jelszavára.    
8.  Mentse az **AzureSqlLinkedService1.json** fájlt. 


## Adatkészletek létrehozása
Az előző lépésben létrehozta az **AzureStorageLinkedService1** és az **AzureSqlLinkedService1** társított szolgáltatásokat egy Azure Storage-fiók és egy Azure SQL Database az **ADFTutorialDataFactoryPSH** data factoryhoz való társításához. Ebben a lépésben két data factory-táblát határoz meg – az **EmpTableFromBlob** és az **EmpSQLTable** táblákat –, amelyek az adattárakban tárolt, az AzureStorageLinkedService1 és az AzureSqlLinkedService1 szolgáltatások által hivatkozott bemeneti és kimeneti adatokat jelölik. Az EmpTableFromBlob táblához meg fogja adni a forrásadatokkal rendelkező blobot tároló blobtárolót, az EmpSQLTable táblához pedig meg fogja adni a kimeneti adatokat tároló SQL-táblát.

### Bemeneti adatkészlet létrehozása

9. A **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal a **Tables** (Táblák) elemre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) elemre.
10. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure Blob** elemet, és kattintson az **Add** (Hozzáadás) elemre.   
10. Cserélje a JSON-szöveget az alábbi szövegre, és mentse az **AzureBlobLocation1.json** fájlt. 

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

### Kimeneti adatkészlet létrehozása

11. A **Solution Explorerben** (Megoldáskezelőben) ismét kattintson a jobb gombbal a **Tables** (Táblák) elemre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre.
12. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza ki a listából az **Azure SQL** elemet, és kattintson az **Add** (Hozzáadás) elemre. 
13. Cserélje a JSON-szöveget az alábbi JSON-szövegre, és mentse az **AzureSqlTableLocation1.json** fájlt.

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

## Folyamat létrehozása 
Eddig bemeneti/kimeneti társított szolgáltatásokat és táblákat hozott létre. Most egy **másolási tevékenységgel** rendelkező folyamatot fog létrehozni, amely az Azure-blobból másol adatokat egy Azure SQL Database-adatbázisba. 


1. A **Megoldáskezelőben** kattintson a jobb gombbal a **Pipelines** (Folyamatok) elemre, mutasson az **Add** (Hozzáadás) elemre, és kattintson a **New Item** (Új elem) lehetőségre.  
15. Az **Add New Item** (Új elem hozzáadása) párbeszédpanelen válassza a **Copy Data Pipeline** (Adatok másolása folyamat) elemet, és kattintson az **Add** (Hozzáadás) parancsra. 
16. Cserélje a JSON-t az alábbi JSON-ra, és mentse a **CopyActivity1.json** fájlt.
            
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

## Data Factory-entitások közzététele/üzembe helyezése
  
18. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a projektre, majd kattintson a **Publish** (Közzététel) parancsra. 
19. Ha megjelenik a **Sign in to your Microsoft account** (Bejelentkezés a Microsoft-fiókba) párbeszédpanel, adja meg az Azure-előfizetéssel rendelkező fiókja hitelesítő adatait, és kattintson a **sign in** (bejelentkezés) elemre.
20. A következő párbeszédpanelnek kell megjelennie:

    ![Publish (Közzététel) párbeszédpanel](./media/data-factory-get-started-using-vs/publish.png)

21. A Configure data factory (Data factory konfigurálása) oldalon tegye a következőket: 
    1. Válassza a **Create New Data Factory** (Új data factory létrehozása) lehetőséget.
    2. A **Name** (Név) mezőbe írja be a következőt: **VSTutorialFactory**.  
    
        > [AZURE.NOTE]  
        > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a közzététel során hibaüzenetet kap a data factory nevéről, módosítsa a data factory nevét (például sajátnévVSTutorialFactory értékre), majd próbálkozzon újra a közzététellel. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
        
    3. A **Subscription** (Előfizetés) mezőben válassza ki a megfelelő előfizetést. 
    4. Válassza ki a data factoryhoz az **erőforráscsoportot**. 
    5. Válassza ki a data factoryhoz a **régiót**. 
    6. Kattintson a **Tovább** gombra a **Publish Items** (Elemek közzététele) oldalra való váltáshoz. 
23. A **Publish Items** (Elemek közzététele) oldalon győződjön meg arról, hogy az összes Data Factory-entitás ki van jelölve, és kattintson a **Tovább** gombra a **Summary** (Összegzés) oldalra való váltáshoz.     
24. Tekintse át az összefoglalót, és kattintson a **Tovább** gombra az üzembehelyezési folyamat elindításához, és a **Deployment Status** (Üzembehelyezési állapot) megtekintéséhez.
25. A **Deployment Status** (Üzembehelyezési állapot) oldalon meg kell jelennie az üzembehelyezési folyamat állapotának. Miután befejeződött az üzembe helyezés, kattintson a Finish (Befejezés) gombra. 

Vegye figyelembe a következőket: 

- Ha a „**This subscription is not registered to use namespace Microsoft.DataFactory**” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel: 

    - Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Az alábbi parancs futtatásával ellenőrizheti a Data Factory-szolgáltató regisztrálását. 
    
            Get-AzureRmResourceProvider
    - Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel automatikusan regisztrálja a szolgáltatót.
-   A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.
-   Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.

## Összefoglalás
Az oktatóanyag során létrehozott egy Azure data factoryt, hogy adatokat másoljon egy Azure-blobból egy Azure SQL Database-adatbázisba. A Visual Studiót használta a data factory, a társított szolgáltatások, az adatkészletek és a folyamat létrehozásához. Az oktatóanyag során a következő főbb lépéseket végezte el:  

1.  Létrehozott egy Azure **data factoryt**.
2.  **Társított szolgáltatásokat** hozott létre:
    1. Egy **Azure Storage** társított szolgáltatást a bemeneti adatokat tároló Azure Storage-fiók társításához.    
    2. Egy **Azure SQL** társított szolgáltatást a kimeneti adatokat tároló Azure SQL Database társításához. 
3.  **Adatkészleteket** hozott létre, amelyek a folyamat bemeneti és kimeneti adatait írják le.
4.  Létrehozott egy **folyamatot** egy **Másolási tevékenységgel**, ahol a **BlobSource** a forrás, az **SqlSink** pedig a fogadó. 


## A Kiszolgókezelő használata data factoryk megtekintéséhez

1. A **Visual Studio** menüjében kattintson a **View** (Megtekintés), majd a **Server Explorer** (Kiszolgálókezelő) elemre.
2. A Server Explorer (Kiszolgálókezelő) ablakban bontsa ki az **Azure**, majd a **Data Factory** elemet. Ha megjelenik a **Sign in to Visual Studio** (Jelentkezzen be a Visual Studióba) üzenet, adja meg az Azure-előfizetéséhez társított **fiókot**, és kattintson a **Continue** (Folytatás) elemre. Adja meg a **jelszót**, és kattintson a **Sign in** (Bejelentkezés) elemre. A Visual Studio megpróbálja lekérni az információkat az előfizetésében elérhető összes Azure data factoryről. Ennek a műveletnek az állapota a **Data Factory Task List** (Data Factory-feladatlista) ablakban jelenik meg.
    ![Server Explorer (Kiszolgálókezelő)](./media/data-factory-get-started-using-vs/server-explorer.png)
3. Ha egy meglévő data factory alapján szeretne létrehozni Visual Studio-projektet, kattintson a jobb gombbal egy dara factoryre, és válassza az Export Data Factory to New Project (Data factory exportálása új projektbe) lehetőséget.
    ![Data factory exportálása VS-projektbe](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Visual Studióhoz készült Data Factory-eszközök frissítése
A Visual Studióhoz készült Data Factory-eszközök frissítéséhez tegye a következőket:

1. Kattintson a menüben a **Tools** (Eszközök) elemre, és válassza az **Extensions and Updates** (Bővítmények és frissítések) lehetőséget. 
2. A bal oldali panelen válassza az **Updates** (Frissítések) elemet, majd válassza a **Visual Studio Gallery** (Visual Studio-gyűjtemény) lehetőséget.
4. Válassza az **Azure Data Factory tools for Visual Studio** lehetőséget, és kattintson az **Update** (Frissítés) elemre. Ha nem látja ezt a bejegyzést, már rendelkezik az eszközök legújabb verziójával. 

A [Monitor datasets and pipeline](data-factory-get-started-using-editor.md#monitor-pipeline) (Adatkészletek és folyamatok figyelése) című cikkben útmutatást találhat arról, hogy hogyan használhatja az Azure Portalt az oktatóanyagban létrehozott folyamatok és adatkészletek figyeléséhez.

## Lásd még:
| Témakör | Leírás |
| :---- | :---- |
| [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) | Ez a cikk részletes információkat tartalmaz a jelen oktatóanyagban használt Másolás tevékenységről. |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) | Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok](data-factory-create-pipelines.md) | Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) | Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban.
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) | Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. 



<!--HONumber=jun16_HO2-->


