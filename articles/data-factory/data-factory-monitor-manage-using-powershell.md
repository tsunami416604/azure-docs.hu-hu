<properties 
    pageTitle="Oktatóanyag: Folyamat létrehozása másolási tevékenységgel az Azure PowerShell használatával" 
    description="Az oktatóanyag során létre fog hozni egy Azure Data Factory-folyamatot egy másolási tevékenységgel az Azure PowerShell használatával." 
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

# Oktatóanyag: Folyamat létrehozása másolási tevékenységgel az Azure PowerShell használatával
> [AZURE.SELECTOR]
- [Az oktatóanyag áttekintése](data-factory-get-started.md)
- [A Data Factory Editor használata](data-factory-get-started-using-editor.md)
- [A Visual Studio használata](data-factory-get-started-using-vs.md)
- [A PowerShell használata](data-factory-monitor-manage-using-powershell.md)
- [A Másolás varázsló használata](data-factory-copy-data-wizard-tutorial.md)

A [Get started with Azure Data Factory][adf-get-started] (Azure Data Factory – első lépések) oktatóanyag bemutatja, hogyan lehet Azure data factoryt létrehozni és felügyelni az [Azure Portal][azure-portal] használatával. Az oktatóanyag során létre fog hozni és felügyelni fog egy Azure Data Factory-folyamatot egy másolási tevékenységgel Azure PowerShell parancsmagok használatával. Az oktatóanyag során létrehozott adat-előállító folyamata egy másolási tevékenység használatával adatokat másol egy Azure-blobból egy Azure SQL Database-adatbázisba.

A Másolás tevékenység végzi az adattovábbítást az Azure Data Factoryben, és a tevékenységet egy olyan, globálisan elérhető szolgáltatás működteti, amely biztonságos, megbízható és méretezhető módon másolja az adatokat a különböző adattárak között. A Másolás tevékenységgel kapcsolatos részletekért tekintse meg a [Data Movement Activities](data-factory-data-movement-activities.md) (Adattovábbítási tevékenységek) című cikket.   

> [AZURE.IMPORTANT] Tekintse meg [Az oktatóanyag áttekintése](data-factory-get-started.md) című cikket, és az oktatóanyag elvégzése előtt hajtsa végre az előfeltételként felsorolt lépéseket.
>   
> Ez a cikk nem tárgyalja az összes Data Factory-parancsmagot. A Data Factory-parancsmagokkal kapcsolatos átfogó dokumentációért tekintse meg a [Data Factory Cmdlet Reference](https://msdn.microsoft.com/library/dn820234.aspx) (Data Factory-parancsmagok referenciája) című cikket.
  

##Előfeltételek
Az oktatóanyag áttekintése című témakörben felsorolt előfeltételek mellett a következőket kell telepítenie:

- **Azure PowerShell**. Kövesse a [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Az Azure PowerShell telepítése és konfigurálása) cikkben foglalt utasításokat az Azure PowerShell telepítéséhez a számítógépre.

Ha az **1.0-s verziónál régebbi** Azure PowerShellt használ, az [itt][old-cmdlet-reference] dokumentált parancsmagokat kell használnia. A Data Factory-parancsmagok használata előtt emellett futtatnia kell az alábbi parancsokat: 

1. Indítsa el az Azure PowerShellt, és futtassa a következő parancsokat. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.
    1. Futtassa az **Add-AzureAccount** parancsot, és adja meg a felhasználónevet és jelszót, amelyekkel az Azure Portalra jelentkezik be.
    2. Futtassa a **Get-AzureSubscription** parancsot a fiókhoz tartozó előfizetések megtekintéséhez.
    3. Futtassa a **Select-AzureSubscription** parancsot a kívánt előfizetés kiválasztásához. Ennek az előfizetésnek egyeznie kell az Azure Portalon használt előfizetéssel.
4. Váltson AzureResourceManager módba, mivel az Azure Data Factory-parancsmagok ebben a módban érhetők el: **Switch-AzureMode AzureResourceManager**.
  

##Az oktatóanyag tartalma
Az alábbi tábla az oktatóanyag során végrehajtott lépéseket és ezek leírását tartalmazza. 

Lépés | Leírás
-----| -----------
[Azure data factory létrehozása](#create-data-factory) | Ebben a lépésben egy Azure data factoryt fog létrehozni **ADFTutorialDataFactoryPSH** néven. 
[Társított szolgáltatások létrehozása](#create-linked-services) | Ebben a lépésben két társított szolgáltatást fog létrehozni, a **StorageLinkedService** és az **AzureSqlLinkedService** szolgáltatást. A StorageLinkedService egy Azure Storage-ot, míg az AzureSqlLinkedService egy Azure SQL Database-t társít az ADFTutorialDataFactoryPSH adat-előállítóhoz.
[Bemeneti és kimeneti adatkészletek létrehozása](#create-datasets) | Ebben a lépésben két adatkészletet (**EmpTableFromBlob** és **EmpSQLTable**) fog létrehozni, amelyek bemeneti és kimeneti táblákként szolgálnak majd a következő lépésben létrehozott ADFTutorialPipeline folyamatban lévő **másolási tevékenységhez**.
[Folyamat létrehozása és futtatása](#create-pipeline) | Ebben a lépésben egy **ADFTutorialPipeline** nevű folyamatot fog létrehozni az **ADFTutorialDataFactoryPSH** data factoryban. . A folyamat része lesz majd egy **másolási tevékenység**, amely egy Azure-blobból másol adatokat egy kimeneti Azure-adatbázistáblába.
[Az adatkészletek és a folyamat figyelése](#monitor-pipeline) | Ebben a lépésben az Azure PowerShell használatával figyeli majd az adatkészleteket és a folyamatot.

## Data factory létrehozása
Ebben a lépésben az Azure PowerShell használatával létrehoz egy **ADFTutorialDataFactoryPSH** nevű Azure data factoryt.

1. Indítsa el az Azure PowerShellt, és futtassa az alábbi parancsot. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.
    - Futtassa a **Login-AzureRmAccount** parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.  
    - Futtassa a **Get-AzureSubscription** parancsot a fiókhoz tartozó előfizetések megtekintéséhez.
    - Futtassa a **Select-AzureSubscription<Name of the subscription>** parancsot a kívánt előfizetés kiválasztásához. Ennek az előfizetésnek egyeznie kell az Azure Portalon használt előfizetéssel.
3. Hozzon létre egy Azure-erőforráscsoportot **ADFTutorialResourceGroup** néven a következő parancs futtatásával.
   
        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Az oktatóanyag különböző lépései során feltételezzük, hogy az **ADFTutorialResourceGroup** elnevezésű erőforráscsoportot használja. Ha másik erőforráscsoportot használ, akkor az oktatóanyagban azt használja az ADFTutorialResourceGroup helyett. 
4. Futtassa a **New-AzureRmDataFactory** parancsmagot, és hozzon létre egy új data factoryt **ADFTutorialDataFactoryPSH** néven.  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

    
Vegye figyelembe a következőket:
 
- Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a **Data factory name “ADFTutorialDataFactoryPSH” is not available** (Az „ADFTutorialDataFactoryPSH” data factory-név nem érhető el) hibaüzenetet kapja, módosítsa a nevet (például: az_ön_neveADFTutorialDataFactoryPSH). Használja ezt az ADFTutorialFactoryPSH helyett az oktatóanyag lépéseinek végrehajtása során. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
- Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének/rendszergazdájának kell lennie.
- A data factory neve később DNS-névként regisztrálható, így nyilvánosan láthatóvá válhat.
- Ha a „**This subscription is not registered to use namespace Microsoft.DataFactory**” (Az előfizetés nem jogosult használni a Microsoft.DataFactory névteret) hibaüzenetet kapja, tegye a következők egyikét, és próbálkozzon újra a közzététellel: 

    - Az Azure PowerShellben futtassa az alábbi parancsot a Data Factory-szolgáltató regisztrálásához. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Az alábbi parancs futtatásával ellenőrizheti a Data Factory-szolgáltató regisztrálását. 
    
            Get-AzureRmResourceProvider
    - Az Azure-előfizetés használatával jelentkezzen be az [Azure Portalra](https://portal.azure.com), és navigáljon egy Data Factory panelre, vagy hozzon létre egy data factoryt az Azure Portalon. Ezzel automatikusan regisztrálja a szolgáltatót.

## Társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Az adattárak lehetnek a Data Factory-folyamatok bemeneti adatait tartalmazó vagy kimeneti adatait tároló Azure Storage-tárolók, Azure SQL Database-adatbázisok vagy helyszíni SQL Server-adatbázisok. A számítási szolgáltatások a bemeneti adatokat feldolgozó és azokból kimeneti adatokat előállító szolgáltatások. 

Ebben a lépésben két társított szolgáltatást fog létrehozni, a **StorageLinkedService** és az **AzureSqlLinkedService** szolgáltatást. Az StorageLinkedService társított szolgáltatás egy Azure Storage-fiókot, az AzureSqlLinkedService pedig egy Azure SQL Database-adatbázist társít az **ADFTutorialDataFactoryPSH** data factoryhoz. Később az oktatóanyagban létre fog hozni egy olyan folyamatot, amely az adatokat a StorageLinkedService egy blobtárolójából az AzureSqlLinkedService egy SQL-táblájába másolja.

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

    Az **accountname** kifejezést cserélje a tárfiókja nevére, az **accountkey** kifejezést pedig az Azure Storage-fiók kulcsára.
2.  Az **Azure PowerShellben** váltson át az **ADFGetStartedPSH** mappára. 
3.  Társított szolgáltatásokat a **New-AzureRmDataFactoryLinkedService** parancsmag használatával hozhat létre. Ehhez, valamint az oktatóanyagban használt többi Data Factory-parancsmaghoz is meg kell adnia értékeket a **ResourceGroupName** és a **DataFactoryName** paraméterek számára. Vagy a **Get-AzureRmDataFactory** használatával lekérhet egy DataFactory-objektumot, és továbbíthatja azt anélkül, hogy minden egyes alkalommal meg kellene adnia a ResourceGroupName és a DataFactoryName értékeket a parancsmag futtatásakor. Futtassa az alábbi parancsot a **Get-AzureRmDataFactory** parancsmag kimenetének hozzárendeléséhez a **$df** változóhoz. 

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.  Most futtassa a **New-AzureRmDataFactoryLinkedService** parancsmagot a **StorageLinkedService** társított szolgáltatás létrehozásához. 

        New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

    Ha nem futtatta volna a **Get-AzureRmDataFactory** parancsmagot és rendelte volna hozzá a kimenetet a **$df** változóhoz, meg kellene adnia ResourceGroupName és a DataFactoryName paraméterek értékeit az alábbiak szerint.   
        
        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

    Ha az oktatóanyag közepén bezárta az Azure PowerShellt, a következő indításakor futtatnia kell a Get-AzureRmDataFactory parancsmagot az oktatóanyag befejezéséhez.

### Társított szolgáltatás létrehozása Azure SQL Database-hez
1.  Hozzon létre egy JSON-fájlt AzureSqlLinkedService.json néven az alábbi tartalommal.

            {
                "name": "AzureSqlLinkedService",
                "properties": {
                    "type": "AzureSqlDatabase",
                    "typeProperties": {
                        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                    }
                }
            }

    A **servername**, **databasename**, **username@servername** és **password** paraméterek értékét cserélje le az Azure SQL-kiszolgáló, az adatbázis és a felhasználói fiók nevére, valamint a felhasználói fiók jelszavára.

2.  Futtassa az alábbi parancsot egy társított szolgáltatás létrehozásához. 
    
        New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

    Erősítse meg, hogy az **Allow access to Azure services** (Azure-szolgáltatások hozzáférésének engedélyezése) beállítás BE van kapcsolva az Azure SQL Serverhez. Az ellenőrzéséhez és bekapcsolásához tegye a következőket:

    1. Kattintson a **BROWSE** (TALLÓZÁS) hubra a bal oldalon, majd az **SQL servers** (SQL Serverek) elemre.
    2. Válassza ki a kiszolgálót, és kattintson a **SETTINGS** (BEÁLLÍTÁSOK) lehetőségre az SQL SERVER (SQL SERVER) panelen.
    3. A **SETTINGS** (BEÁLLÍTÁSOK) panelen kattintson a **Firewall** (Tűzfal) elemre.
    4. A **Firewall settings** (Tűzfalbeállítások) panelen kattintson az **ON** (BE) kapcsolóra az **Allow access to Azure services** (Azure-szolgáltatások hozzáférésének engedélyezése) beállítás mellett.
    5. Kattintson az **ACTIVE** (AKTÍV) központra a bal oldalon, hogy visszaváltson a **Data Factory** panelre, amelyen korábban volt.
    

## Adatkészletek létrehozása

Az előző lépésben létrehozta a **StorageLinkedService** és az **AzureSqlLinkedService** társított szolgáltatásokat egy Azure Storage-fiók és egy Azure SQL Database társításához az **ADFTutorialDataFactoryPSH** data factoryhoz. Ebben a lépésben a következő lépésben létrehozandó folyamat részét képező másolási tevékenység ki- és bemenetét jelölő adatkészleteket fog létrehozni. 

A tábla egy téglalap alakú adatkészlet, és jelenleg ez az egyetlen támogatott adatkészlettípus. Az oktatóanyagban lévő bemeneti tábla egy blobtárolóra hivatkozik az Azure Storage-ban, amelyre a StorageLinkedService mutat, a kimeneti tábla pedig egy SQL-táblára hivatkozik az Azure SQL Database-ben, amelyre az AzureSqlLinkedService mutat.  

### Az Azure Blob Storage és az Azure SQL Database előkészítése az oktatóanyaghoz
Ha már elvégezte a [Get started with Azure Data Factory][adf-get-started] (Azure Data Factory – első lépések) cikkben foglalt oktatóanyagot, hagyja ki ezt a lépést. 

Az alábbi lépéseket kell végrehajtania az Azure Blob Storage és az Azure SQL Database előkészítéséhez ehhez az oktatóanyaghoz. 
 
* Hozzon létre egy blobtárolót **adftutorial** néven az Azure Blob Storage-ban, amelyre a **StorageLinkedService** mutat. 
* Hozza létre és töltse fel az **emp.txt** szöveges fájlt blobként az **adftutorial** tárolóba. 
* Hozzon létre egy táblát **emp** néven az Azure SQL Database-ben abban az Azure SQL Database-ben, amelyre az **AzureSqlLinkedService** mutat.


1. Indítsa el a Jegyzettömböt, majd illessze be az alábbi szöveget, és mentse **emp.txt** néven a **C:\ADFGetStartedPSH** mappába a merevlemezen. 

        John, Doe
        Jane, Doe
                
2. Az [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) vagy egy hasonló eszköz használatával hozza létre az **adftutorial** tárolót, és töltse fel az **emp.txt** fájlt a tárolóba.

    ![Azure Storage Explorer][image-data-factory-get-started-storage-explorer]
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

    Ha a Visual Studio 2013 van telepítve a számítógépen: az Azure Portalon ([http://portal.azure.com](http://portal.sazure.com)), kattintson a **BROWSE** (TALLÓZÁS) központra a bal oldalon, majd kattintson az **SQL servers** (SQL Server-kiszolgálók) lehetőségre, válassza ki az adatbázist, és kattintson az **Open in Visual Studio** (Megnyitás a Visual Studióban) gombra az eszközsávon az Azure SQL Server-kiszolgálóhoz való csatlakozáshoz és a szkript futtatásához. Ha az ügyfél számára nem engedélyezett az Azure SQL Server elérése, konfigurálnia kell az Azure SQL Server tűzfalát, hogy engedélyezze a hozzáférést a gép számára (IP-cím). Az Azure SQL Server-tűzfal konfigurálásának lépéseit lásd a fenti cikkben.
        
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
    
    Vegye figyelembe a következőket: 
    
    - Az adatkészlet **típusa** **AzureBlob** értékre van állítva.
    - a **linkedServiceName** tulajdonság **StorageLinkedService** értékre van állítva. 
    - A **folderPath** (mappaútvonal) tulajdonság az **adftutorial** tárolóra van állítva. 
    - a **fileName** tulajdonság **emp.txt** értékre van állítva. Ha nem adja meg a blob nevét, a tárolóban lévő összes blob adata bemeneti adatnak minősül.  
    - A formátum **type** (típus) tulajdonsága **TextFormat** (Szövegformátum) értékre van állítva.
    - A szövegfájlban két mező található – a **FirstName** (Utónév) és a **LastName** (Vezetéknév) –, amelyeket egy vessző karakter választ el (**columnDelimiter**). 
    - Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre), így a Data Factory szolgáltatás óránként keres bemeneti adatokat a megadott blobtároló (**adftutorial**) gyökérmappájában.

    Ha nem adja meg a **fileName** (fájlnév) paramétert a **bemeneti** **táblához**, a bemeneti mappában (**folderPath**) lévő összes fájl/blob bemenetnek minősül. Ha megadja a fileName paramétert a JSON-fájlban, csak a megadott fájl/blob minősül bemenetnek. 
 
    Ha nem adja meg a **fileName** (fájlnév) paramétert a **kimeneti táblához**, a **folderPath** útvonalon előállított fájlok a következő formátumban lesznek elnevezve: Data.<Guid\>.txt (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

2.  A Data Factory-adatkészlet létrehozásához futtassa az alábbi parancsot.

        New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json

### Kimeneti adatkészlet létrehozása
A lépés ezen szakaszában létrehozza az **EmpSQLTable** nevű kimeneti táblát, amely az Azure SQL Database szolgáltatásnak az **AzureSqlLinkedService** társított szolgáltatás által jelölt SQL-táblájára mutat (**emp**). A folyamat átmásolja az adatokat a bemeneti blobból az **emp** táblába. 

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

     Vegye figyelembe a következőket: 
    
    * Az adatkészlet **type** tulajdonsága **AzureSqlTable** értékre van állítva.
    * A **linkedServiceName** tulajdonság **AzureSqlLinkedService** értékre van állítva.
    * A **tablename** tulajdonság **emp** értékre van állítva.
    * Az adatbázis emp táblájában három oszlop van – **ID**, **FirstName** és **LastName** –, az ID azonban azonosítóoszlop, ezért itt csak a **FirstName** és **LastName** tulajdonságokat kell megadnia.
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
                "start": "2015-12-09T00:00:00Z",
                "end": "2015-12-10T00:00:00Z",
                "isPaused": false
              }
            }

    Vegye figyelembe a következőket:

    - A tevékenységek szakaszban csak egyetlen tevékenység van, amelynek a **típusa** **Copy** értékre van beállítva.
    - A tevékenység bemenetének beállítása **EmpTableFromBlob**, a kimeneté pedig **EmpSQLTable**.
    - Az **átalakítás** szakaszban forrástípusként a **BlobSource**, fogadótípusként pedig az **SqlSink** érték van megadva.

    A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra. Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2014-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk. 
    
    Ha nem adja meg az **end** (befejezés) tulajdonság értékét, akkor a rendszer a „**kezdő időpont + 48 óra**” számítással határozza meg azt. A folyamat határozatlan ideig történő futtatásához adja meg a **9/9/9999** értéket az **end** (befejezés) tulajdonsághoz.
    
    A fenti példában 24 adatszelet lesz, mert a rendszer óránként létrehoz egy adatszeletet.
    
    A JSON-tulajdonságokkal kapcsolatos információkért lásd: [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) (Referencia a JSON-parancsprogramokhoz).
2.  A Data Factory-tábla létrehozásához futtassa az alábbi parancsot. 
        
        New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Gratulálunk!** Sikeresen létrehozott egy Azure data factoryt, társított szolgáltatásokat, táblákat és egy folyamatot, valamint ütemezte is a folyamatot.

## Folyamat figyelése
Ebben a lépésben az Azure PowerShell használatával figyeli egy Azure data factory eseményeit.

1.  Futtassa a **Get-AzureRmDataFactory** parancsot, és rendelje a kimenetet egy $df változóhoz.

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.  A **Get-AzureRmDataFactorySlice** parancs futtatásával hívja le az összes szelet részleteit a folyamat **EmpSQLTable** nevű kimeneti táblájában.  

        Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

    A **StartDateTime** paraméter év, hónap és nap részeire írja be az aktuális évet, hónapot és napot. Ennek egyeznie kell a **Start** (Kezdés) értékkel a folyamat JSON-fájljában. 

    24 szeletet kell látnia, éjféltől másnap éjfélig. 
    
    **Első szelet:**

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : ADFTutorialDataFactoryPSH
        TableName         : EmpSQLTable
        Start             : 3/3/2015 12:00:00 AM
        End               : 3/3/2015 1:00:00 AM
        RetryCount        : 0
        Status            : Waiting
        LatencyStatus     :
        LongRetryCount    : 0

    **Utolsó szelet:**

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : ADFTutorialDataFactoryPSH
        TableName         : EmpSQLTable
        Start             : 3/4/2015 11:00:00 PM
        End               : 3/4/2015 12:00:00 AM
        RetryCount        : 0
        Status            : Waiting
        LatencyStatus     : 
        LongRetryCount    : 0

3.  A **Get-AzureRmDataFactoryRun** parancs futtatásával kérje le egy **adott** szelet tevékenységfuttatásainak részleteit. Módosítsa a **StartDateTime** paraméter értékét, hogy megegyezzen a fenti kimenetből származó szelet **Start** (Kezdés) idejével. A **StartDateTime** értékét [ISO formátumban](http://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2014-03-03T22:00:00Z.

        Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

    A következőhöz hasonló kimenetnek kell megjelennie:

        Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
        ResourceGroupName   : ADFTutorialResourceGroup
        DataFactoryName     : ADFTutorialDataFactoryPSH
        TableName           : EmpSQLTable
        ProcessingStartTime : 3/3/2015 11:03:28 PM
        ProcessingEndTime   : 3/3/2015 11:04:36 PM
        PercentComplete     : 100
        DataSliceStart      : 3/8/2015 10:00:00 PM
        DataSliceEnd        : 3/8/2015 11:00:00 PM
        Status              : Succeeded
        Timestamp           : 3/8/2015 11:03:28 PM
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
| [Folyamatok](data-factory-create-pipelines.md) | Ennek a cikknek a segítségével megismerheti a Azure Data Factory folyamatait és tevékenységeit, és megtudhatja, hogyan hozhat létre velük teljes körű, adatvezérelt munkafolyamatokat saját forgatókönyvéhez vagy vállalkozásához. |
| [Adatkészletek](data-factory-create-datasets.md) | Ennek a cikknek a segítségével megismerheti az adatkészleteket az Azure Data Factoryban.
| [Folyamatok figyelése és felügyelete a Monitoring App használatával](data-factory-monitor-manage-app.md) | Ez a cikk ismerteti, hogyan figyelheti és felügyelheti a folyamatokat, illetve hogyan kereshet bennük hibákat a Monitoring & Management App használatával. 



[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[adf-get-started]: data-factory-get-started.md
[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 


<!--HONumber=jun16_HO2-->


