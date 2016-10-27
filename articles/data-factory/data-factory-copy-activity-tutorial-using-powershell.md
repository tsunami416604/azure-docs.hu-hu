<properties 
    pageTitle="Oktatóanyag: Folyamat létrehozása másolási tevékenységgel az Azure PowerShell használatával | Microsoft Azure" 
    description="Az oktatóanyag során létrehoz egy Azure Data Factory-folyamatot egy másolási tevékenységgel az Azure PowerShell használatával." 
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


# Oktatóanyag: Folyamat létrehozása másolási tevékenységgel az Azure PowerShell használatával
> [AZURE.SELECTOR]
- [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
- [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Az oktatóanyag során létrehoz és felügyel egy Azure Data Factory-folyamatot egy másolási tevékenységgel Azure PowerShell parancsmagok használatával. Az oktatóanyag során létrehozott adat-előállító folyamata egy másolási tevékenység használatával adatokat másol egy Azure-blobból egy Azure SQL Database-adatbázisba.

A másolási tevékenység végzi az adattovábbítást az Azure Data Factoryban. A tevékenységet egy globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és skálázható módon másolja az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket.   

> [AZURE.IMPORTANT] 
> Tekintse meg [Az oktatóanyag áttekintése](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikket, és az oktatóanyag elvégzése előtt hajtsa végre az **előfeltételként** felsorolt lépéseket.
>   
> Ez a cikk nem tárgyalja az összes Data Factory-parancsmagot. A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentációért tekintse meg a [Data Factory Cmdlet Reference](https://msdn.microsoft.com/library/dn820234.aspx) (Data Factory-parancsmagok referenciája) című cikket.
  

##Előfeltételek
Az oktatóanyag áttekintése című témakörben felsorolt előfeltételek mellett az **Azure PowerShellt** is telepítenie kell. Kövesse a [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre.

##Az oktatóanyag tartalma
Az alábbi tábla az oktatóanyag során végrehajtott lépéseket és ezek leírását tartalmazza. 

Lépés | Leírás
-----| -----------
[Azure data factory létrehozása](#create-data-factory) | Ebben a lépésben egy Azure data factoryt hoz létre **ADFTutorialDataFactoryPSH** néven. 
[Társított szolgáltatások létrehozása](#create-linked-services) | Ebben a lépésben két társított szolgáltatást hoz létre, a **StorageLinkedService** és az **AzureSqlLinkedService** szolgáltatást. A StorageLinkedService egy Azure Storage-ot, míg az AzureSqlLinkedService egy Azure SQL Database-t társít az ADFTutorialDataFactoryPSH adat-előállítóhoz.
[Bemeneti és kimeneti adatkészletek létrehozása](#create-datasets) | Ebben a lépésben két adatkészletet határoz meg (**EmpTableFromBlob** és **EmpSQLTable**). Ezek az adatkészletek bemeneti és kimeneti táblákként szolgálnak a következő lépésben létrehozott ADFTutorialPipeline folyamatban található **másolási tevékenységhez**.
[Folyamat létrehozása és futtatása](#create-pipeline) | Ebben a lépésben egy **ADFTutorialPipeline** nevű folyamatot hoz létre az **ADFTutorialDataFactoryPSH** data factoryban. A folyamat része egy **másolási tevékenység**, amely egy Azure-blobból másol adatokat egy kimeneti Azure-adatbázistáblába.
[Az adatkészletek és a folyamat figyelése](#monitor-pipeline) | Ebben a lépésben az Azure PowerShell használatával figyeli meg az adatkészleteket és a folyamatot.

## Data factory létrehozása
Ebben a lépésben az Azure PowerShell használatával létrehoz egy **ADFTutorialDataFactoryPSH** nevű Azure data factoryt.

1. Indítsa el a **PowerShellt**. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell a parancsokat.
    1. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
    
            Login-AzureRmAccount   
    2. Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

            Get-AzureRmSubscription 
    3. Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje a **&lt;NameOfAzureSubscription**&gt; kifejezést az Azure-előfizetése nevére.

            Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

3. Hozzon létre egy Azure-erőforráscsoportot **ADFTutorialResourceGroup** néven a következő parancs futtatásával.
   
        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Az oktatóanyag különböző lépései során feltételezzük, hogy az **ADFTutorialResourceGroup** elnevezésű erőforráscsoportot használja. Ha másik erőforráscsoportot használ, akkor az oktatóanyagban azt használja az ADFTutorialResourceGroup helyett. 
4. Futtassa a **New-AzureRmDataFactory** parancsmagot, és hozzon létre egy új data factoryt **ADFTutorialDataFactoryPSH** néven.  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

    
Vegye figyelembe a következő pontokat:
 
- Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet (például sajátnévADFTutorialDataFactoryPSH-ra). Használja ezt az ADFTutorialFactoryPSH helyett az oktatóanyag lépéseinek végrehajtása során. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
    
        Data factory name “ADFTutorialDataFactoryPSH” is not available
- Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.
- A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.
- Ha a „**This subscription is not registered to use namespace Microsoft.DataFactory**” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel: 

    - Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Az alábbi parancs futtatásával ellenőrizheti, hogy a Data Factory-szolgáltató regisztrálva van-e. 
    
            Get-AzureRmResourceProvider
    - Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel a művelettel automatikusan regisztrálja a szolgáltatót.

## Társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Az adattárak lehetnek a Data Factory-adatcsatornák bemeneti adatait tartalmazó vagy kimeneti adatait tároló Azure Storage-tárolók, Azure SQL Database-adatbázisok vagy helyszíni SQL Server-adatbázisok. A számítási szolgáltatások a bemeneti adatokat feldolgozó és azokból kimeneti adatokat előállító szolgáltatások. 

Ebben a lépésben két társított szolgáltatást hoz létre, a **StorageLinkedService** és az **AzureSqlLinkedService** szolgáltatást. Az StorageLinkedService társított szolgáltatás egy Azure Storage-fiókot, az AzureSqlLinkedService pedig egy Azure SQL Database-adatbázist társít az **ADFTutorialDataFactoryPSH** data factoryhoz. Később az oktatóanyagban létre fog hozni egy olyan folyamatot, amely az adatokat a StorageLinkedService egy blobtárolójából az AzureSqlLinkedService egy SQL-táblájába másolja.

### Társított szolgáltatás létrehozása Azure Storage-fiókhoz
1.  Hozzon létre egy JSON-fájlt **StorageLinkedService.json** néven a **C:\ADFGetStartedPSH** mappában az alábbi tartalommal. Ha még nem létezik, hozza létre az ADFGetStartedPSH mappát.

            {
                "name": "StorageLinkedService",
                "properties": {
                    "type": "AzureStorage",
                    "typeProperties": {
                        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                    }
                }
            }

    Az **accountname** és az **accountkey** kifejezés helyére írja be Azure Storage-tárfiókja nevére, illetve kulcsát.
2.  Az **Azure PowerShellben** váltson át az **ADFGetStartedPSH** mappára. 
3.  Társított szolgáltatásokat a **New-AzureRmDataFactoryLinkedService** parancsmag használatával hozhat létre. Ehhez, valamint az oktatóanyagban használt többi Data Factory-parancsmaghoz is meg kell adnia értékeket a **ResourceGroupName** és a **DataFactoryName** paraméterek számára. Vagy a **Get-AzureRmDataFactory** használatával lekérhet egy DataFactory-objektumot, és továbbíthatja azt anélkül, hogy minden egyes alkalommal meg kellene adnia a ResourceGroupName és a DataFactoryName értékeket a parancsmag futtatásakor. Futtassa az alábbi parancsot a **Get-AzureRmDataFactory** parancsmag kimenetének hozzárendeléséhez a **$df** változóhoz. 

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.  Most futtassa a **New-AzureRmDataFactoryLinkedService** parancsmagot a **StorageLinkedService** társított szolgáltatás létrehozásához. 

        New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

    Ha nem futtatta volna a **Get-AzureRmDataFactory** parancsmagot és rendelte volna hozzá a kimenetet a **$df** változóhoz, meg kellene adnia ResourceGroupName és a DataFactoryName paraméterek értékeit az alábbiak szerint.   
        
        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

    Ha az oktatóanyag lépései során bezárta az Azure PowerShellt, a következő indításakor futtatnia kell a Get-AzureRmDataFactory parancsmagot az oktatóanyag befejezéséhez.

### Társított szolgáltatás létrehozása Azure SQL Database-hez
1.  Hozzon létre egy JSON-fájlt AzureSqlLinkedService.json néven az alábbi tartalommal.

            {
                "name": "AzureSqlLinkedService",
                "properties": {
                    "type": "AzureSqlDatabase",
                    "typeProperties": {
                        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                    }
                }
            }

    A **servername**, **databasename**, **username@servername** és **password** paraméterek értékét cserélje le az Azure SQL-kiszolgáló, az adatbázis és a felhasználói fiók nevére, valamint a felhasználói fiók jelszavára.

2.  Futtassa az alábbi parancsot egy társított szolgáltatás létrehozásához. 
    
        New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

    Erősítse meg, hogy az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás BE van kapcsolva az Azure SQL Serverhez. Az ellenőrzéséhez és bekapcsolásához hajtsa végre a következő lépéseket:

    1. Kattintson a **BROWSE** (TALLÓZÁS) hubra a bal oldalon, majd az **SQL servers** (SQL Serverek) elemre.
    2. Válassza ki a kiszolgálót, és kattintson a **SETTINGS** (BEÁLLÍTÁSOK) lehetőségre az SQL SERVER (SQL SERVER) panelen.
    3. A **SETTINGS** (BEÁLLÍTÁSOK) panelen kattintson a **Firewall** (Tűzfal) elemre.
    4. A **Tűzfalbeállítások** panelen kattintson a **BE** kapcsolóra az **Azure-szolgáltatások hozzáférésének engedélyezése** beállítás mellett.
    5. Kattintson az **ACTIVE** (AKTÍV) központra a bal oldalon, hogy visszaváltson a **Data Factory** panelre, amelyen korábban volt.
    

## Adatkészletek létrehozása

Az előző lépésben létrehozta a **StorageLinkedService** és az **AzureSqlLinkedService** társított szolgáltatásokat egy Azure Storage-fiók és egy Azure SQL Database társításához az **ADFTutorialDataFactoryPSH** data factoryhoz. Ebben a lépésben a következő lépésben létrehozandó folyamat részét képező másolási tevékenység ki- és bemenetét jelölő adatkészleteket hoz létre. 

A tábla egy téglalap alakú adatkészlet, és jelenleg ez az egyetlen támogatott adatkészlettípus. Az oktatóanyagban lévő bemeneti tábla egy blobtárolóra hivatkozik az Azure Storage-ban, amelyre a StorageLinkedService mutat. A kimeneti tábla egy SQL-táblára hivatkozik az Azure SQL Database-ben, amelyre az AzureSqlLinkedService mutat.  

### Az Azure Blob Storage és az Azure SQL Database előkészítése az oktatóanyaghoz
Ha már elvégezte a [Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Adatok másolása Blob Storage-ból SQL-adatbázisba) cikkben foglalt oktatóanyagot, hagyja ki ezt a lépést. 

Hajtsa végre az alábbi lépéseket az Azure Blob Storage és az Azure SQL-adatbázis előkészítéséhez ehhez az oktatóanyaghoz. 
 
* Hozzon létre egy blobtárolót **adftutorial** néven az Azure Blob Storage-ban, amelyre a **StorageLinkedService** mutat. 
* Hozza létre és töltse fel az **emp.txt** szöveges fájlt blobként az **adftutorial** tárolóba. 
* Hozzon létre egy táblát **emp** néven az Azure SQL Database-ben abban az Azure SQL Database-ben, amelyre az **AzureSqlLinkedService** mutat.


1. Indítsa el a Jegyzettömböt, majd illessze be az alábbi szöveget, és mentse **emp.txt** néven a **C:\ADFGetStartedPSH** mappába a merevlemezen. 

        John, Doe
        Jane, Doe
                
2. Az [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) vagy egy hasonló eszköz használatával hozza létre az **adftutorial** tárolót, és töltse fel az **emp.txt** fájlt a tárolóba.

    ![Azure Storage Explorer](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. A következő SQL-szkript használatával hozza létre az **emp** táblát az Azure SQL Database-ben.  


        CREATE TABLE dbo.emp 
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

    Ha az SQL Server 2014 van telepítve a számítógépen: kövesse a Managing Azure SQL Database using SQL Server Management Studio (Az Azure SQL Database szolgáltatás felügyelete az SQL Server Management Studio használatával) című cikk [Step 2: Connect to SQL Database of the Managing Azure SQL Database using SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md) (Kapcsolódás az SQL Database-hez) című lépésében foglalt utasításokat Azure SQL Server-kiszolgálóhoz való csatlakozáshoz, és futtassa az SQL-szkriptet.

    Ha az ügyfél számára nem engedélyezett az Azure SQL Server elérése, konfigurálnia kell az Azure SQL Server tűzfalát, hogy engedélyezze a hozzáférést a gép számára (IP-cím). Az Azure SQL Server-tűzfal konfigurálásának lépéseit lásd [ebben a cikkben](../sql-database/sql-database-configure-firewall-settings.md).
        
### Bemeneti adatkészlet létrehozása 
A tábla egy sémával rendelkező, téglalap alakú adatkészlet. Ebben a lépésben létrehozza az **EmpBlobTable** nevű táblát, amely a **StorageLinkedService** társított szolgáltatás által jelölt Azure Storage-blobtárolójára mutat. Ez a blobtároló (**adftutorial**) tartalmazza a bemeneti adatokat az **emp.txt** fájlban. 

1.  Hozzon létre egy JSON-fájlt **EmpBlobTable.json** néven a **C:\ADFGetStartedPSH** mappában az alábbi tartalommal:

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
                "linkedServiceName": "StorageLinkedService",
                "typeProperties": {
                  "fileName": "emp.txt",
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
    
    Vegye figyelembe a következő pontokat: 
    
    - Az adatkészlet **típusa** **AzureBlob** értékre van állítva.
    - a **linkedServiceName** tulajdonság **StorageLinkedService** értékre van állítva. 
    - A **folderPath** (mappaútvonal) tulajdonság az **adftutorial** tárolóra van állítva. 
    - a **fileName** tulajdonság **emp.txt** értékre van állítva. Ha nem adja meg a blob nevét, a tárolóban lévő összes blob adata bemeneti adatnak minősül.  
    - A formátum **type** (típus) tulajdonsága **TextFormat** (Szövegformátum) értékre van állítva.
    - A szövegfájlban két mező található – a **FirstName** (Utónév) és a **LastName** (Vezetéknév) –, amelyeket egy vessző karakter választ el (**columnDelimiter**). 
    - Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre). Így a Data Factory óránként keres bemeneti adatokat a megadott blobtároló (**adftutorial**) gyökérmappájában.

    Ha nem adja meg a **fileName** (fájlnév) paramétert a **bemeneti** **táblához**, a bemeneti mappában (**folderPath**) lévő összes fájl/blob bemenetnek minősül. Ha megadja a fileName paramétert a JSON-fájlban, csak a megadott fájl/blob minősül bemenetnek. 
 
    Ha nem adja meg a **fileName** (fájlnév) paramétert a **kimeneti táblához**, a **folderPath** útvonalon előállított fájlok a következő formátumban lesznek elnevezve: Data.<Guid\>.txt (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Ha a **folderPath** és a **fileName** tulajdonságokat dinamikusan szeretné beállítani a **SliceStart** változó időpontja alapján, használja a **partitionedBy** tulajdonságot. A következő példában a folderPath tulajdonság a SliceStart (a feldolgozás alatt álló szelet kezdő időpontja) változó Év, Hónap és Nap értékeit, a fileName tulajdonság pedig a SliceStart változó Óra értékét használja. Ha például a szelet előállítása a 2016-10-20T08:00:00 időpontban kezdődik, a folderName tulajdonság beállítása wikidatagateway/wikisampledataout/2016/10/20, a fileName beállítása pedig 08.csv lesz. 

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

2.  A Data Factory-adatkészlet létrehozásához futtassa az alábbi parancsot.

        New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json

### Kimeneti adatkészlet létrehozása
Ebben a lépésben egy kimeneti adatkészletet hoz létre **EmpSQLTable** néven. Ez az adatkészlet egy SQL-táblára (**emp**) mutat az Azure SQL Database-ben, amelyet az **AzureSqlLinkedService** jelöl. A folyamat átmásolja az adatokat a bemeneti blobból az **emp** táblába. 

1.  Hozzon létre egy JSON-fájlt **EmpSQLTable.json** néven a **C:\ADFGetStartedPSH** mappában az alábbi tartalommal.
        
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

     Vegye figyelembe a következő pontokat: 
    
    * Az adatkészlet **type** tulajdonsága **AzureSqlTable** értékre van állítva.
    * A **linkedServiceName** tulajdonság **AzureSqlLinkedService** értékre van állítva.
    * A **tablename** tulajdonság **emp** értékre van állítva.
    * Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName**. Az ID azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.
    * Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre).  A Data Factory szolgáltatás óránként előállít egy kimeneti adatszeletet az Azure SQL Database **emp** táblájában.

2.  A Data Factory-adatkészlet létrehozásához futtassa az alábbi parancsot. 
    
        New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json


## Folyamat létrehozása
Ebben a lépésben létrehoz egy **Másolási tevékenységgel** rendelkező folyamatot, amely bemenetként az **EmpTableFromBlob**, kimenetként pedig az **EmpSQLTable** táblát használja.

1.  Hozzon létre egy JSON-fájlt **ADFTutorialPipeline.json** néven a **C:\ADFGetStartedPSH** mappában az alábbi tartalommal. 
    
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
                        "type": "SqlSink"
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
                "start": "2016-08-09T00:00:00Z",
                "end": "2016-08-10T00:00:00Z",
                "isPaused": false
              }
            }

    Vegye figyelembe a következő pontokat:

    - A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **Copy** értékre van beállítva.
    - A tevékenység bemenetének beállítása **EmpTableFromBlob**, a kimeneté pedig **EmpSQLTable**.
    - Az **átalakítás** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva.

    A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra. Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2016-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. 
    
    Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9/9/9999** értéket az **end** (befejezés) tulajdonsághoz.
    
    A példában 24 adatszelet van, mert a rendszer óránként létrehoz egy adatszeletet.
    
    A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) (Referencia a JSON-parancsprogramokhoz).
2.  A Data Factory-tábla létrehozásához futtassa az alábbi parancsot. 
        
        New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Gratulálunk!** Sikeresen létrehozott egy Azure data factoryt, társított szolgáltatásokat, táblákat és egy folyamatot, valamint ütemezte is a folyamatot.

## Folyamat figyelése
Ebben a lépésben az Azure PowerShell használatával figyeli egy Azure data factory eseményeit.

1.  Futtassa a **Get-AzureRmDataFactory** parancsot, és rendelje a kimenetet egy $df változóhoz.

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.  A **Get-AzureRmDataFactorySlice** parancs futtatásával hívja le az összes szelet részleteit a folyamat **EmpSQLTable** nevű kimeneti táblájában.  

        Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00

    A **StartDateTime** paraméter év, hónap és nap részeire írja be az aktuális évet, hónapot és napot. Ennek a beállításnak egyeznie kell a **Start** (Kezdés) értékkel a folyamat JSON-fájljában. 

    24 szeletet kell látnia, éjféltől másnap éjfélig. 
    
    **Példa a kimenetre:**

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : ADFTutorialDataFactoryPSH
        TableName         : EmpSQLTable
        Start             : 8/9/2016 12:00:00 AM
        End               : 8/9/2016 1:00:00 AM
        RetryCount        : 0
        Status            : Waiting
        LatencyStatus     :
        LongRetryCount    : 0

3.  A **Get-AzureRmDataFactoryRun** parancs futtatásával kérje le egy **adott** szelet tevékenységfuttatásainak részleteit. Módosítsa a **StartDateTime** paraméter értékét, hogy megegyezzen a kimenetből származó szelet **Start** (Kezdés) idejével. A **StartDateTime** értékét [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. 

        Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00

    A következő, mintaként szolgáló kimenethez hasonló kimenetnek kell megjelennie:

        Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
        ResourceGroupName   : ADFTutorialResourceGroup
        DataFactoryName     : ADFTutorialDataFactoryPSH
        TableName           : EmpSQLTable
        ProcessingStartTime : 8/9/2016 11:03:28 PM
        ProcessingEndTime   : 8/9/2016 11:04:36 PM
        PercentComplete     : 100
        DataSliceStart      : 8/9/2016 10:00:00 PM
        DataSliceEnd        : 8/9/2016 11:00:00 PM
        Status              : Succeeded
        Timestamp           : 8/9/2016 11:03:28 PM
        RetryAttempt        : 0
        Properties          : {}
        ErrorMessage        :
        ActivityName        : CopyFromBlobToSQL
        PipelineName        : ADFTutorialPipeline
        Type                : Copy

A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentációért lásd a [Data Factory Cmdlet Reference][cmdlet-reference] (Data Factory-parancsmagok referenciája) című cikket. 

## Összefoglalás
Az oktatóanyag során létrehozott egy Azure data factoryt, hogy adatokat másoljon egy Azure-blobból egy Azure SQL Database-adatbázisba. A PowerShellt használta a data factory, a társított szolgáltatások, az adatkészletek és a folyamat létrehozásához. Az oktatóanyag során a következő főbb lépéseket végezte el:  

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



[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 


<!--HONumber=Oct16_HO3-->


