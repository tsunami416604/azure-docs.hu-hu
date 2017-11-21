---
title: "Adatok másolása az SQL Serverről a Blob Storage-ba az Azure Data Factory használatával | Microsoft Docs"
description: "Megismerheti az adatok a helyszíni adattárolókból az Azure-beli felhőbe, az Azure Data Factory saját üzemeltetésű integrációs moduljával történő másolásának folyamatát."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: afd7735712d03110a67509a7e94d336219a65b34
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Oktatóanyag: Adatok másolása helyszíni SQL Serverről az Azure Blob Storage-ba
Ebben az oktatóanyagban az Azure PowerShell használatával egy Data Factory-folyamatot hozunk létre adatok egy helyszíni SQL Server-adatbázisból egy Azure Blob-tárolóba történő másolására. Létrehozzuk és alkalmazzuk az Azure Data Factory egy saját üzemeltetésű integrációs modulját (IR)/ Ez lehetővé teszi a helyszíni és a felhőalapú adattárolók integrálását.  Az adat-előállítók egyéb eszközökkel/SDK-kkal való létrehozásával kapcsolatban lásd [rövid útmutatóinkat](quickstart-create-data-factory-dot-net.md).

Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md). 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Azure Storage-beli társított szolgáltatások létrehozása. 
> * SQL Server- és Azure Blob-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

### <a name="sql-server-2014-or-2016"></a>SQL Server 2014 vagy 2016. 
Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk **forrásadattárként**. Hozzon létre egy **emp** nevű táblát az SQL Server-adatbázisban, és szúrjon be a táblába néhány mintabejegyzést.

1. Indítsa el az **SQL Server Management Studiót**. Ha SQL Server 2016 verziót használ, lehetséges, hogy külön telepítenie kell az SQL Server Management Studiót a [Letöltőközpontból](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Csatlakozzon az SQL-kiszolgálóhoz a hitelesítő adataival. 
3. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal az **Adatbázisok** elemre, majd válassza az **Új adatbázis** elemet. Az **Új adatbázis** párbeszédpanelen adjon **nevet** az új adatbázisnak, majd kattintson az **OK** gombra. 
4. Futtassa az alábbi lekérdezést azon az adatbázison, amely létrehozza az **emp** táblát. A fanézetben kattintson a jobb gombbal a létrehozott **adatbázisra**, majd válassza az **Új lekérdezés** elemet. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Futtassa az alábbi parancsokat az adatbázison, majd illesszen be mintaadatokat a táblába:

    ```sql
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="azure-storage-account"></a>Azure Storage-fiók
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk **cél-/fogadóadattárként**. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket.

#### <a name="get-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben a rövid útmutatóban az Azure Storage-fiók nevét és kulcsát használjuk. Az alábbi eljárás bemutatja a tárfióknév és -kulcs beszerzéséhez szükséges lépéseket. 

1. Nyisson meg egy webböngészőt, és keresse fel az [Azure Portalt](https://portal.azure.com). Jelentkezzen be az Azure-beli felhasználónevével és jelszavával. 
2. Kattintson a **További szolgáltatások >** elemre a bal oldali menüben, állítson be egy szűrőt a **Tárfiók** kulcsszóval, majd válassza a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. A tárfiókok listájában állítson be szűrőt a tárfiók nevéhez (ha szükséges), majd válassza ki a **tárfiókját**. 
4. A **Tárfiók** oldalon a menüben válassza a **Hozzáférési kulcsok** elemet.

    ![Tárfióknév és -kulcs beszerzése](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Másolja a **Tárfiók neve** és az **1. kulcs** mezők értékét a vágólapra. Illessze be őket a Jegyzettömbbe, vagy bármely más szerkesztőbe, majd mentse a fájlt. Használja az oktatóprogramban használt tárfióknevet és -kulcsot. 

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása 
Ebben a szakaszban egy adftutorial nemű blobtárolót hoz létre az Azure Blob Storage-ban. 

1. Ha még nincs a gépén, telepítse az [Azure Storage Explorert](https://azure.microsoft.com/features/storage-explorer/). 
2. Indítsa el a gépén a **Microsoft Azure Storage Explorert**.   
3. A **Csatlakozás az Azure Storage-hoz** ablakban válassza a **Tárfiók nevének és kulcsának használata** lehetőséget, és kattintson a **Tovább** gombra. Ha nem látja a **Csatlakozás az Azure Storage-hoz** ablakot, a fanézetben kattintson a jobb gombbal a **Tárfiókok** elemre, majd kattintson a **Csatlakozás az Azure Storage-hoz** elemre. 

    ![Csatlakozás az Azure Storage-hoz](media/tutorial-hybrid-copy-powershell/storage-explorer-connect-azure-storage.png)
4. A **Csatolás a név és a kulcs használatával** ablakban illessze be az előző lépésben mentett **Fióknév** és **Fiókkulcs** értékeket. Ezután kattintson a **Tovább** gombra. 
5. A **Kapcsolatok összegzése** ablakban kattintson a **Csatlakozás** elemre.
6. Győződjön meg arról, hogy a tárfiók megjelenik a **(Helyi és csatolt)** -> **Tárfiókok** faszerkezetes nézetében. 
7. Bontsa ki a **Blobtárolók** elemet, és győződjön meg róla, hogy az **adftutorial** nevű blobtároló nem létezik. Ha már létezik, hagyja ki a következő, a tároló létrehozására vonatkozó lépéseket. 
8. Kattintson a jobb gombbal a **Blobtárolók** elemre, majd válassza a **Blobtároló létrehozása** lehetőséget.

    ![Blobtároló létrehozása](media/tutorial-hybrid-copy-powershell/stroage-explorer-create-blob-container-menu.png)
9. Adja a tárolónak az **adftutorial** nevet, és nyomja le az **ENTER** billentyűt. 
10. Győződjön meg arról, hogy az **adftutorial** tároló van kijelölve a fanézetben. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése
Ha még nincs a gépén, telepítse az Azure PowerShell legújabb verzióját. 

1. A webböngészőben lépjen az [Azure SDK letöltések és SDK-k](https://azure.microsoft.com/downloads/) lapra. 
2. A **Parancssori eszközök** -> **PowerShell** szakaszban kattintson a **Windows-telepítés** elemre. 
3. Futtassa az **MSI**-fájlt az Azure PowerShell telepítéséhez. 

Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe
Indítsa el a gépén a **PowerShellt**. Az Azure PowerShellt hagyja megnyitva a rövid útmutató végéig. Ha bezárja és újra megnyitja a programot, akkor újra le kell futtatnia a parancsokat.

1. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt Azure-beli felhasználói nevét és jelszavát:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Ha több Azure-előfizetéssel is rendelkezik, futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le a **SubscriptionId** kifejezést az Azure-előfizetés azonosítójára:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az adat-előállító neveként használhat. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Adjon meg egy változót az adat-előállító helyéhez: 

    ```powershell
    $location = "East US"
    ```
4. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd próbálkozzon újra. Ha meg szeretné osztani az erőforráscsoportot másokkal, folytassa a következő lépéssel.  
5. Az adat-előállító létrehozásához futtassa az alábbi **Set-AzureRmDataFactoryV2** parancsmagot: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory-példányok létrehozásához az Azure-előfizetés **közreműködőjének** vagy **rendszergazdájának** kell lennie.
* A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

## <a name="create-a-self-hosted-ir"></a>Saját üzemeltetésű integrációs modul létrehozása

Ebben a szakaszban egy saját üzemeltetésű integrációs modult hozhat létre, és társíthatja azt egy helyszíni csomóponttal (géppel).

1. Hozzon létre egy változót az integrációs modul nevéhez. 

    ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
    ```
1. Hozzon létre egy saját üzemeltetésű integrációs modult. Használjon egy egyedi nevet, amennyiben már létezne egy integrációs modul ezen a néven.

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Itt látható a minta kimenete:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Futtassa az alábbi parancsot a létrehozott integrációs modul állapotának lekéréséhez.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Itt látható a minta kimenete:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Futtassa az alábbi parancsot a hitelesítési kulcsok lekéréséhez, hogy a saját üzemeltetésű integrációs modult regisztrálhassa a Data Factory-szolgáltatásban a felhőben. Másolja be az egyik kulcsot a saját üzemeltetésű integrációs modul regisztrálásához.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Itt látható a minta kimenete:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

## <a name="install-integration-runtime"></a>Integrációs modul telepítése
1. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) a saját üzemeltetésű integrációs modult egy helyi windowsos gépre, és futtassa a telepítést. 
2. Az **Üdvözli a Microsoft Integrációs modul telepítővarázsló** oldalon kattintson a **Tovább** gombra.  
3. A **Végfelhasználói licencszerződés** oldalon fogadja el a felhasználási feltételeket és a licencszerződést, majd kattintson a **Tovább** gombra. 
4. A **Célmappa** lapon kattintson a **Tovább** gombra. 
5. **A Microsoft Integrációs modul telepítésre kész** oldalon kattintson a **Telepítés** elemre. 
6. Ha figyelmeztető üzenet jelenik meg azzal kapcsolatban, hogy a számítógép használaton kívül automatikus alvó módba lépésre vagy hibernálásra lesz konfigurálva, kattintson az **OK** gombra. 
7. **A Microsoft Integrációs modul telepítővarázslójának befejezése** lapon kattintson a **Befejezés** gombra.
8. Az **Integrációs modul regisztrálása (Saját üzemeltetésű)** lapon illessze be az előző szakaszban mentett kulcsot, és kattintson a **Regisztráció** gombra. 

   ![Integrációs modul regisztrálása](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. Az integrációs modul sikeres regisztrációja esetén a következő üzenet jelenik meg:

   ![Sikeres regisztráció](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. Az **Új integrációs (Saját üzemeltetésű) modulcsomópont** lapon kattintson a **Tovább** gombra. 

    ![Új integrációs modulcsomópont lap](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. Az **Intranetes kommunikációs csatorna** lapon kattintson a **Kihagyás** gombra. Választhat TLS/SSL-hitelesítést a csomóponton belüli kommunikációhoz többcsomópontos integrációs modul környezetben. 

    ![Intranetes kommunikációs csatorna lap](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. Az **Integrációs modul regisztrálása (Saját üzemeltetésű)** lapon kattintson a **Konfigurációkezelő indítása** gombra. 
6. A csomópont a felhőszolgáltatáshoz való csatlakozásakor a következő oldal jelenik meg:

   ![Csomópont csatlakoztatva](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage-beli társított szolgáltatás (cél/fogadó) létrehozása

1. Hozzon létre egy **AzureStorageLinkedService.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában az alábbi tartalommal: Ha még nem létezne, hozza létre az ADFv2Tutorial nevű mappát.  

    > [!IMPORTANT]
    > A fájl mentése előtt az &lt;accountName&gt; és az &lt;accountKey&gt; kifejezést cserélje le az Azure Storage-fiók nevére és kulcsára.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```
2. Az **Azure PowerShellben** váltson az **ADFv2Tutorial** mappára.

   Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot az **AzureStorageLinkedService** társított szolgáltatás létrehozásához. Az oktatóanyagban alkalmazott parancsmagok a **ResourceGroupName** és a **DataFactoryName** paraméter értékeit fogja felvenni. Vagy a Set-AzureRmDataFactoryV2 parancsmag használatával lekért **DataFactory** objektumot is átadhatja anélkül, hogy minden egyes alkalommal meg kellene adnia a ResourceGroupName és a DataFactoryName értéket a parancsmag futtatásakor.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Itt látható egy mintakimenet:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server-alapú társított szolgáltatás létrehozása és titkosítása (forrás)

1. Hozzon létre egy **SqlServerLinkedService.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal: a fájl mentése előtt a **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** és **&lt;password>** értékeket cserélje le a SQL Server értékeire. 

    > [!IMPORTANT]
    > Cserélje le az **&lt;integration** **runtime** **name>** értéket az integrációs modul nevére.

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. A helyszíni saját üzemeltetésű integrációs modulban a bizalmas hasznos JSON-adatok titkosításához futtassa a **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** parancsot, és adja át a fenti hasznos JSON-adatokat. A titkosítás a hitelesítő adatokat az adatvédelmi API (DPAPI) segítségével titkosítja. A titkosított hitelesítő adatokat a saját üzemeltetésű integrációs modul csomópontján helyileg tárolja (helyi gépen). A kimenő hasznos adatok átirányíthatóak egy másik JSON-fájlba (ebben az esetben az „encryptedLinkedService.json” fájlba), amely titkosított hitelesítő adatokat tartalmaz.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Az **SqlServerLinkedService** létrehozásához az előző lépésben szereplő JSON használatával futtassa a következő parancsot:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben olyan bemeneti és kimeneti adatkészleteket hoz létre, amelyek a másolási művelet be- és kimeneti adatait képezik majd (Helyszíni SQL Server-adatbázis => Azure Blob Storage).

### <a name="create-a-dataset-for-source-sql-database"></a>Adatkészlet létrehozása a forrás SQL-adatbázishoz

1. Hozzon létre egy **SqlServerDataset.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Az **SqlServerDataset** adatkészlet létrehozásához futtassa a **Set-AzureRmDataFactoryV2Dataset** parancsmagot.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Itt látható a minta kimenete:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Adatkészlet létrehozása a fogadó Azure Blob Storage-hoz

1. Hozzon létre egy **AzureBlobDataset.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal:

    > [!IMPORTANT]
    > Ez a példakód azt feltételezi, hogy az Azure Blob Storage-ban rendelkezik egy **adftutorial** nevű tárolóval.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Az **AzureBlobDataset** adatkészlet létrehozásához futtassa a **Set-AzureRmDataFactoryV2Dataset** parancsmagot.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Itt látható a minta kimenete:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Folyamatok létrehozása

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Az „SqlServerToBlobPipeline” folyamat létrehozása

1. Hozzon létre egy **SqlServerToBlobPipeline.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Az **SQLServerToBlobPipeline** folyamat létrehozásához futtassa a **Set-AzureRmDataFactoryV2Pipeline** parancsmagot.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Itt látható a minta kimenete:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Folyamat futásának indítása és monitorozása

1. Indítsa el az „SQLServerToBlobPipeline” folyamat futását, és őrizze meg a folyamat futtatási azonosítóját későbbi monitorozás céljából.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Az „**SQLServerToBlobPipeline**” folyamat futási állapotának folyamatos ellenőrzéséhez futtassa a következő szkriptet, majd nyomtassa ki a végső eredményt.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Itt látható a példa futtatás kimenete:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Az „**SQLServerToBlobPipeline**” folyamat futtatási azonosítójának lekéréséhez és a tevékenységfuttatás részletes eredményének áttekintéséhez tegye a következőket.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Itt látható a példa futtatás kimenete:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```
## <a name="verify-the-output"></a>Kimenet ellenőrzése
A folyamat automatikusan létrehozza a `fromonprem` nevű kimeneti mappát az `adftutorial` blobtárolóban. Győződjön meg arról, hogy a **dbo.emp.txt** fájl megjelenik a kimeneti mappában. Az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) ellenőrizze, hogy létrejött-e a kimenet. 

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Azure Storage-beli társított szolgáltatások létrehozása. 
> * SQL Server- és Azure Blob-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

Az Azure Data Factory által támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

Folytassa a következő oktatóanyaggal, amelyben az adatok egy forrásból egy célhelyre történő tömeges másolását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok tömeges másolása](tutorial-bulk-copy.md)
