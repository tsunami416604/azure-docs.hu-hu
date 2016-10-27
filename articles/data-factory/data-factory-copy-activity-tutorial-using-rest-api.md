<properties 
    pageTitle="Oktatóanyag: Másolási tevékenységgel ellátott adatcsatorna létrehozása a REST API használatával | Microsoft Azure" 
    description="Ez az oktatóanyag bemutatja, hogyan hozhat létre másolási tevékenységgel rendelkező Azure Data Factory-adatcsatornákat a REST API használatával." 
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


# Oktatóanyag: Másolási tevékenységgel ellátott adatcsatorna létrehozása a REST API használatával
> [AZURE.SELECTOR]
- [Áttekintés és előfeltételek](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Másolás varázsló](data-factory-copy-data-wizard-tutorial.md)
- [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure Resource Manager-sablon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és figyelhet meg Azure-adat-előállítót a REST API segítségével. Az adat-előállítóban szereplő adatcsatorna másolási tevékenységgel másolja az adatokat az Azure Blob Storage-ből az Azure SQL Database-be.

> [AZURE.NOTE] 
> Ez a cikk nem tárgyalja a Data Factoryhoz használható REST API egészét. A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentációért tekintse meg a [Data Factory REST API Reference](https://msdn.microsoft.com/library/azure/dn906738.aspx) (Data Factory REST API referenciája) című cikket.
  

## Előfeltételek

- Tekintse meg [Az oktatóanyag áttekintése](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című cikket, és hajtsa végre az **előfeltételként** felsorolt lépéseket.
- Telepítse gépére a [Curl](https://curl.haxx.se/dlwiz/) eszközt. A Curl eszköz a REST-parancsokkal együtt az adat-előállító létrehozására használható. 
- Az [ebben a cikkben](../resource-group-create-service-principal-portal.md) szereplő utasításokat követve végezze el a következőket: 
    1. Hozzon létre egy **ADFCopyTutorialApp** nevű webalkalmazást az Azure Active Directoryban.
    2. Szerezze be az **ügyfél-azonosítót** és a **titkos kulcsot**. 
    3. Szerezze be a **bérlőazonosítót**. 
    4. Rendelje az **ADFCopyTutorialApp** alkalmazáshoz a **Data Factory közreműködője** szerepkört.  
- Telepítse az [Azure PowerShellt](../powershell-install-configure.md).  
- Indítsa el a **PowerShellt**, és futtassa az alábbi parancsot. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell a parancsokat.
    1. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
    
            Login-AzureRmAccount   
    2. Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

            Get-AzureRmSubscription 
    3. Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. A **&lt;NameOfAzureSubscription**&gt; helyére írja be Azure-előfizetése nevét. 

            Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    1. Hozzon létre egy **ADFTutorialResourceGroup** nevű Azure-erőforráscsoportot. Ehhez futtassa a következő parancsot a PowerShellben.  

            New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

        Ha az erőforráscsoport már létezik, adja meg, hogy frissítse azt a rendszer (Y), vagy hagyja változatlanul (N). 

        Az oktatóanyag különböző lépései során feltételezzük, hogy az ADFTutorialResourceGroup elnevezésű erőforráscsoportot használja. Ha másik erőforráscsoportot használ, használja ehelyett saját erőforráscsoportja nevét, amikor az oktatóanyag az ADFTutorialResourceGroup csoportra utal.
  
## JSON-definíciók létrehozása
Hozza létre a következő JSON-fájlokat abban a mappában, ahol a curl.exe is található. 

### datafactory.json 
> [AZURE.IMPORTANT] Globálisan egyedi nevet kell használnia, ezért érdemes lehet előtagot/utótagot adnia az ADFCopyTutorialDF névhez, hogy az egyedivé váljon. 

    {  
        "name": "ADFCopyTutorialDF",  
        "location": "WestUS"
    }  

### azurestoragelinkedservice.json
> [AZURE.IMPORTANT] Az **accountname** és az **accountkey** kifejezés helyére írja be Azure Storage-tárfiókja nevére, illetve kulcsát. A tárelérési kulcs lekérésével kapcsolatos információk: [Tárelérési kulcsok megtekintése, másolása és újragenerálása](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }

### azuersqllinkedservice.json
> [AZURE.IMPORTANT] A **servername**, a **databasename**, a **username** és a **password** kifejezés helyére írja be Azure SQL-kiszolgálója nevét, az SQL-adatbázis nevét, a felhasználói fiókot, valamint a fiók jelszavát.  

    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "",
            "typeProperties": {
                "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
            }
        }
    }


### inputdataset.json

    {
      "name": "AzureBlobInput",
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

A JSON-definíció az **AzureBlobInput** nevű adatkészletet határozza meg, amely az adatcsatorna adott tevékenységéhez tartozó bemeneti adatokat jelöli. Ezenfelül meghatározza, hogy a bemeneti adatok az **adftutorial** nevű Blob-tároló **emp.txt** nevű fájljában találhatóak. 

 Vegye figyelembe a következő pontokat: 

- Az adatkészlet **típusa** **AzureBlob** értékre van állítva.
- A **linkedServiceName** tulajdonság **AzureStorageLinkedService** értékre van állítva. 
- A **folderPath** értéke az **adftutorial** tároló lesz, a **fileName** értéke pedig az **emp.txt**.  
- A formátum **type** (típus) tulajdonsága **TextFormat** (Szövegformátum) értékre van állítva.
- A szövegfájlban két mező található – a **FirstName** (Utónév) és a **LastName** (Vezetéknév) –, amelyeket egy vessző karakter választ el (**columnDelimiter**). 
- Az **availability** értéke **hourly** lesz (a frequency értéke hour, az interval értéke pedig 1). Ez azt jelenti, hogy a Data Factory óránként keres bemeneti adatokat a megadott Blob-tároló (**adftutorial**) gyökérkönyvtárában. 

Ha nem adja meg a **fileName** paramétert a bemeneti adatkészlethez, a bemeneti mappában (**folderPath**) található összes fájl/blob bemenetnek minősül. Ha megadja a fileName paramétert a JSON-ban, csak a megadott fájl/blob minősül bemenetnek.

Ha nem adja meg a **fileName** paramétert a **kimeneti táblához**, a **folderPath** útvonalon előállított fájlok elnevezéséhez a rendszer a következő formátumot használja: Data.&lt;Guid&gt;.txt (példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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


### outputdataset.json
    
    {
      "name": "AzureSqlOutput",
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


A JSON-definíció az **AzureSqlOutput** nevű adatkészletet határozza meg, amely az adatcsatorna adott tevékenységéhez tartozó eredményadatokat jelöli. Ezenfelül azt is meghatározza, hogy az eredményeket az AzureSqlLinkedService által meghatározott adatbázis **emp** táblája tárolja. Az **availability** paraméter meghatározza, hogy a kimeneti adatkészlet előállítása óránként történik (frequency: hour és interval: 1 beállítás).

Vegye figyelembe a következő pontokat: 

- Az adatkészlet **type** (típus) tulajdonsága **AzureSQLTable** értékre van állítva.
- A **linkedServiceName** tulajdonság **AzureSqlLinkedService** értékre van állítva.
- A **tablename** tulajdonság **emp** értékre van állítva.
- Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName**. Az ID azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.
- Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre).  A Data Factory szolgáltatás óránként előállít egy kimeneti adatszeletet az Azure SQL Database **emp** táblájában.

### pipeline.json

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
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
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
        "start": "2016-08-12T00:00:00Z",
        "end": "2016-08-13T00:00:00Z"
      }
    }


Vegye figyelembe a következő pontokat:

- A tevékenységek szakaszban csak egyetlen olyan tevékenység van, amelynek a **type** (típus) tulajdonsága **CopyActivity** értékre van állítva.
- A tevékenység bemenetének beállítása **AzureBlobInput**, a kimeneté pedig **AzureSqlOutput**.
- Az **átalakítás** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva.

A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra. Azt is megteheti, hogy a dátum-időpont paraméternek csak a dátum részét adja meg, az időpont részét pedig kihagyja. Megadhatja például a „2015-02-03” értéket, amely a következőnek felel meg: „2015-02-03T00:00:00Z”.

Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2014-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. 

Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9999-09-09** értéket az **end** (befejezés) tulajdonsághoz.

A fenti példában 24 adatszelet jön létre, mivel a nap minden órájában készül egy adatszelet.
    
> [AZURE.NOTE] Az előző példában használt JSON-tulajdonságokkal kapcsolatos részletekért lásd az [Anatomy of a Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) (Az adatcsatornák működése) című témakört.

## Globális változók beállítása

Adja meg saját értékeit, majd az Azure PowerShellben hajtsa végre a következő parancsokat:

> [AZURE.IMPORTANT] Az ügyfél-azonosító, a titkos ügyfélkód, a bérlőazonosító és az előfizetés-azonosító beszerzésével kapcsolatban olvassa el az [Előfeltételek](#prerequisites) című fejezetet.   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "ADFCopyTutorialDF"

## Hitelesítés az AAD segítségével 
Az Azure Active Directory (AAD) segítségével végzett hitelesítéshez futtassa a következő parancsokat. 

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 

## Data factory létrehozása

Ebben a lépésben egy **ADFCopyTutorialDF** nevű Azure-adatelőállítót fog létrehozni. A data factory egy vagy több folyamattal rendelkezhet. A folyamaton belül egy vagy több tevékenység lehet. Ilyen például a másolási tevékenység, amely adatokat másol a forrásból a célként megadott adattárba, vagy a HDInsight Hive tevékenység, amely Hive-parancsprogram futtatásával átalakítja a bemeneti adatokat, így biztosít védelmet a kimeneti adatoknak. Az adat-előállító létrehozásához futtassa az alábbi parancsokat: 

1. Rendelje a parancsot a **cmd** nevű változóhoz. 

    Ellenőrizze, hogy itt is azt az adatelőállító-nevet adja-e meg, amit a **datafactory.json** fájlban (ADFCopyTutorialDF). 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. Az **Invoke-Command** használatával futtassa a parancsot.

        $results = Invoke-Command -scriptblock $cmd;
3. Tekintse meg az eredményeket. Ha az adat-előállító sikeresen létrejött, az **eredmények** között meg kell jelennie az adat-előállítóhoz tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.  

        Write-Host $results

Vegye figyelembe a következő szempontokat:
 
- Az Azure Data Factory nevének globálisan egyedinek kell lennie. Ha az eredményeknél a következő hibaüzenet jelenik meg: **A(z) „ADFCopyTutorialDF” Data factory nem érhető el**, tegye a következőket:  
    1. Módosítsa a nevet (például yournameADFCopyTutorialDF) a **datafactory.json** fájlban.
    2. Az első parancsnál, amikor a **$cmd** változóhoz értéket rendel, cserélje le az ADFCopyTutorialDF értéket az új névre, és futtassa le így a parancsot. 
    3. A következő két parancs lefuttatásával hívja meg a REST API-t, amely létrehozza az adat-előállítót, és kiírja a művelet eredményeit. 
    
    A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
- Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.
- Az adat-előállító neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá tehető.
- Ha a „**This subscription is not registered to use namespace Microsoft.DataFactory**” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel: 

    - Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Az alábbi parancs futtatásával ellenőrizheti, hogy a Data Factory-szolgáltató regisztrálva van-e. 
    
            Get-AzureRmResourceProvider
    - Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel a művelettel automatikusan regisztrálja a szolgáltatót.

A folyamat létrehozása előtt először létre kell hoznia néhány Data Factory-entitást. Először hozza létre a társított szolgáltatást, amely összekapcsolja a forrásként és célként használt adattárakat az Ön adattárával. Majd definiálja a bemeneti és kimeneti adatkészleteket, amelyek adatként szolgálnak a társított adattárakban. Végül hozza létre az adatcsatornát egy tevékenységgel, amely ezeket az adatkészleteket használja.

## Társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Az adattárak lehetnek a Data Factory-adatcsatornák bemeneti adatait tartalmazó vagy kimeneti adatait tároló Azure Storage-tárolók, Azure SQL Database-adatbázisok vagy helyszíni SQL Server-adatbázisok. A számítási szolgáltatások a bemeneti adatokat feldolgozó és azokból kimeneti adatokat előállító szolgáltatások. 

Ebben a lépésben létrehozza a következő két társított szolgáltatást: **AzureStorageLinkedService** és **AzureSqlLinkedService**. Az AzureStorageLinkedService társított szolgáltatás egy Azure Storage-fiókot, az AzureSqlLinkedService pedig egy Azure SQL-adatbázist társít az **ADFCopyTutorialDF** adat-előállítóhoz. Az oktatóanyag későbbi részében létrehoz egy adatcsatornát, amely adatokat másol az AzuretStorageLinkedService Blob-tárolójából az AzureSqlLinkedService SQL-táblájába.

### Azure Storage társított szolgáltatás létrehozása
Ebben a lépésben társítani fogja Azure Storage-fiókját az adat-előállítóhoz. Ebben az oktatóanyagban az Azure Storage-fiókot használjuk a bemeneti adatok tárolására. 

1. Rendelje a parancsot a **cmd** nevű változóhoz. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Az **Invoke-Command** használatával futtassa a parancsot.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Tekintse meg az eredményeket. Ha a társított szolgáltatás sikeresen létrejött, az **eredmények** között meg kell jelennie a társított szolgáltatáshoz tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.
  
        Write-Host $results

### Azure SQL társított szolgáltatás létrehozása
Ebben a lépésben társítani fogja az Azure SQL-adatbázist az adat-előállítóhoz. Ebben az oktatóanyagban ugyanezt az Azure SQL-adatbázist használjuk a kimeneti adatok tárolására.

1. Rendelje a parancsot a **cmd** nevű változóhoz. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. Az **Invoke-Command** használatával futtassa a parancsot.
 
        $results = Invoke-Command -scriptblock $cmd;
3. Tekintse meg az eredményeket. Ha a társított szolgáltatás sikeresen létrejött, az **eredmények** között meg kell jelennie a társított szolgáltatáshoz tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.
  
        Write-Host $results

## Adatkészletek létrehozása

Az előző lépésben létrehozta az **AzureStorageLinkedService** és az **AzureSqlLinkedService** társított szolgáltatást, hogy egy Azure Storage-fiókot és egy Azure SQL-adatbázist társítson az **ADFCopyTutorialDF** adat-előállítóhoz. Ebben a lépésben a következő lépésben létrehozandó adatcsatorna részét képező másolási tevékenység be- és kimenetét képviselő adatkészleteket fogjuk létrehozni. 

Az oktatóanyagban használt bemeneti adatkészlet arra az Azure Storage-fiókban található Blob-tárolóra utal, amelyre az AzureStorageLinkedService mutat. A kimeneti adatkészlet arra az Azure SQL-adatbázisban található SQL-táblára utal, amelyre az AzureSqlLinkedService mutat.  

### Az Azure Blob Storage és az Azure SQL Database előkészítése az oktatóanyaghoz
Hajtsa végre az alábbi lépéseket az Azure Blob Storage és az Azure SQL-adatbázis előkészítéséhez ehhez az oktatóanyaghoz. 
 
* Hozza létre az **adftutorial** nevű Blob-tárolót az Azure Blob Storage-ban, amelyre az **AzureStorageLinkedService** mutat. 
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

    Ha az SQL Server 2014 van telepítve a számítógépen: kövesse [Managing Azure SQL Database using SQL Server Management Studio] (Az Azure SQL Database szolgáltatás felügyelete az SQL Server Management Studio használatával) [sql-management-studio] című cikk Connect to SQL Database of the Managing Azure SQL Database using SQL Server Management Studio (Kapcsolódás az SQL Database-hez) című 2. lépésében foglalt utasításokat Azure SQL Server-kiszolgálóhoz való csatlakozáshoz, és futtassa az SQL-szkriptet.

    Ha az ügyfél számára nem engedélyezett az Azure SQL-kiszolgáló elérése, úgy kell beállítania az Azure SQL-kiszolgáló tűzfalát, hogy engedélyezze a hozzáférést a gép (IP-cím) számára. Az Azure SQL Server-tűzfal konfigurálásának lépéseit lásd [ebben a cikkben](../sql-database/sql-database-configure-firewall-settings.md).
        
### Bemeneti adatkészlet létrehozása 
Ebben a lépésben létrehozza az **AzureBlobInput** nevű adatkészletet, amely egy, az Azure Storage-ben található Blob-tárolóra mutat, amelyet az **AzureStorageLinkedService** társított szolgáltatás képvisel. Ez a blobtároló (**adftutorial**) tartalmazza a bemeneti adatokat az **emp.txt** fájlban. 

1. Rendelje a parancsot a **cmd** nevű változóhoz. 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Az **Invoke-Command** használatával futtassa a parancsot.

        $results = Invoke-Command -scriptblock $cmd;
3. Tekintse meg az eredményeket. Ha az adatkészlet sikeresen létrejött, az **eredmények** között meg kell jelennie az adatkészlethez tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.
  
        Write-Host $results

### Kimeneti adatkészlet létrehozása
Ebben a lépésben létrehozzuk az **AzureSqlOutput** nevű kimeneti táblát. Ez az adatkészlet az Azure SQL-adatbázis egyik SQL-táblájára mutat (**emp**), amelyet az **AzureSqlLinkedService** képvisel. A folyamat átmásolja az adatokat a bemeneti blobból az **emp** táblába. 

1. Rendelje a parancsot a **cmd** nevű változóhoz.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. Az **Invoke-Command** használatával futtassa a parancsot.

        $results = Invoke-Command -scriptblock $cmd;
3. Tekintse meg az eredményeket. Ha az adatkészlet sikeresen létrejött, az **eredmények** között meg kell jelennie az adatkészlethez tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.
  
        Write-Host $results 

## Folyamat létrehozása
Ebben a lépésben létrehoz egy **másolási tevékenységgel** rendelkező adatcsatornát, amely bemenetként az **AzureBlobInput**, kimenetként az **AzureSqlOutput** táblát használja.

1. Rendelje a parancsot a **cmd** nevű változóhoz.
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Az **Invoke-Command** használatával futtassa a parancsot.

        $results = Invoke-Command -scriptblock $cmd;
3. Tekintse meg az eredményeket. Ha az adatkészlet sikeresen létrejött, az **eredmények** között meg kell jelennie az adatkészlethez tartozó JSON-nak. Ellenkező esetben hibaüzenet jelenik meg.  

        Write-Host $results

**Gratulálunk!** Sikeresen létrehozott egy Azure-adatelőállítót, amelyhez adatokat az Azure Blob Storage-ből az Azure SQL-adatbázisba másoló adatcsatorna tartozik.

## Folyamat figyelése
Ebben a lépésben a Data Factory REST API segítségével figyelheti meg az adatcsatorna által készített szeleteket.

    $ds ="AzureSqlOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;


    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }

Futtassa addig a parancsokat, amíg nem lát **Kész** vagy **Sikertelen** állapotú szeletet. Ha a szelet kész állapotban van, ellenőrizze az Azure SQL-adatbázis **emp** táblájában a kimeneti adatokat. 

Az egyes szeletekhez a rendszer két sornyi adatot másol a forrásfájlból az Azure SQL-adatbázis emp táblájába. Ezért az összes szelet sikeres feldolgozását követően (azaz amikor az összes szelet Kész állapotban van) 24 új bejegyzésnek kell szerepelnie az emp táblában. 


## Összefoglalás
Az oktatóanyag során a REST API segítségével létrehozott egy Azure-adatelőállítót, amely adatokat másol egy Azure Blob-tárolóból egy Azure SQL-adatbázisba. Az oktatóanyag során a következő főbb lépéseket végezte el:  

1.  Létrehozott egy Azure **data factoryt**.
2.  **Társított szolgáltatásokat** hozott létre:
    1. Egy Azure Storage társított szolgáltatást a bemeneti adatokat tároló Azure Storage-fiók csatlakoztatásához.    
    2. Egy Azure SQL társított szolgáltatást a kimeneti adatokat tároló Azure SQL Database csatlakoztatásához. 
3.  **Adatkészleteket** hozott létre, amelyek az adatcsatorna bemeneti és kimeneti adatait írják le.
4.  Létrehozott egy másolási tevékenységgel ellátott **adatcsatornát**, ahol a BlobSource a forrás, az SqlSink pedig a fogadó. 

## Lásd még:
| Témakör | Leírás |
| :---- | :---- |
| [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) | Ez a cikk részletes információkat tartalmaz a jelen oktatóanyagban használt Másolás tevékenységről. |
| [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) | Ez a cikk ismerteti az Azure Data Factory-alkalmazásmodell ütemezési és végrehajtási aspektusait. |
| [Folyamatok](data-factory-create-pipelines.md) | Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
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


