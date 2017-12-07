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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ba47f3e3f331929b884f27f49bf6e484ff363765
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Oktatóanyag: Adatok másolása helyszíni SQL Serverről az Azure Blob Storage-ba
Ebben az oktatóanyagban az Azure PowerShell használatával egy Data Factory-folyamatot hozunk létre adatok egy helyszíni SQL Server-adatbázisból egy Azure Blob-tárolóba történő másolására. Létrehozhat és alkalmazhat egy saját üzemeltetésű integrációs modult, amely adatokat helyez át a helyszíni és a felhőalapú adattárolók között. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md). 

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Azure Storage-beli társított szolgáltatások létrehozása. 
> * SQL Server- és Azure Blob-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

## <a name="prerequisites"></a>Előfeltételek
### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="azure-roles"></a>Azure-szerepkörök
Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie. Az Azure Portalon kattintson a **felhasználónévre** a jobb felső sarokban, majd válassza az **Engedélyek** elemet az előfizetésben található engedélyek megtekintéséhez. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. Ha szeretne példautasításokat látni egy felhasználó szerepkörhöz adására, olvassa el a [Szerepkörök hozzáadása](../billing/billing-add-change-azure-subscription-administrator.md) című cikket.

### <a name="sql-server-201420162017"></a>SQL Server 2014/2016/2017
Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk **forrásadattárként**. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol egy helyszíni SQL Server-adatbázisból (forrás) egy Azure Blob Storage tárolóba (fogadó). Hozzon létre egy **emp** nevű táblát az SQL Server-adatbázisban, és szúrjon be a táblába néhány mintabejegyzést. 

1. Indítsa el az **SQL Server Management Studiót** a gépen. Ha még nem rendelkezik az SQL Server Management Studióval, telepítse a [Letöltőközpontból](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Csatlakozzon az SQL-kiszolgálóhoz a hitelesítő adataival. 
3. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal az **Adatbázisok** elemre, majd válassza az **Új adatbázis** elemet. Az **Új adatbázis** párbeszédpanelen adjon **nevet** az új adatbázisnak, majd kattintson az **OK** gombra. 
4. Futtassa az alábbi lekérdezésszkriptet az adatbázison, amely létrehozza az **emp** táblát, és beszúr néhány mintaadatot. A fanézetben kattintson a jobb gombbal a létrehozott **adatbázisra**, majd válassza az **Új lekérdezés** elemet. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )

    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure Storage-fiók
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk **cél-/fogadóadattárként**. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol a helyszíni SQL Server-adatbázisból (forrás) az Azure Blob Storage tárolóba (fogadó). 

#### <a name="get-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben az oktatóanyagban az Azure Storage-fiók nevét és kulcsát használjuk. Az alábbi eljárás bemutatja a tárfióknév és -kulcs beszerzéséhez szükséges lépéseket. 

1. Nyisson meg egy webböngészőt, és keresse fel az [Azure Portalt](https://portal.azure.com). Jelentkezzen be az Azure-beli felhasználónevével és jelszavával. 
2. Kattintson a **További szolgáltatások >** elemre a bal oldali menüben, állítson be egy szűrőt a **Tárfiók** kulcsszóval, majd válassza a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. A tárfiókok listájában állítson be szűrőt a tárfiók nevéhez (ha szükséges), majd válassza ki a **tárfiókját**. 
4. A **Tárfiók** oldalon a menüben válassza a **Hozzáférési kulcsok** elemet.

    ![Tárfióknév és -kulcs beszerzése](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Másolja a **Tárfiók neve** és az **1. kulcs** mezők értékét a vágólapra. Illessze be őket a Jegyzettömbbe, vagy bármely más szerkesztőbe, majd mentse a fájlt. Használja az oktatóprogramban használt tárfióknevet és -kulcsot. 

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása 
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre az Azure Blob Storage-ban. 

1. A **Storage-fiók** lapon váltson át az **Áttekintés** panelre, majd kattintson a **Blobok** elemre. 

    ![A Blobok elem választása](media/tutorial-hybrid-copy-powershell/select-blobs.png)
1. A **Blob service** lapon kattintson az eszköztár **+ Tároló** elemére. 

    ![Tároló hozzáadása gomb](media/tutorial-hybrid-copy-powershell/add-container-button.png)
3. Az **Új tároló** párbeszédablakban adja meg az **adftutorial** nevet, és kattintson az **OK** gombra. 

    ![Tárolónév megadása](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)
4. A tárolók listájában kattintson az **adftutorial** elemre.  

    ![A tároló kiválasztása](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)
5. Ne zárja be az **adftutorial** **tároló** lapját. A segítségével ellenőrizheti az oktatóanyag eredményét. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia.

    ![Tároló lap](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>A PowerShell telepítése
Ha még nincs a gépén, telepítse a PowerShell legújabb verzióját. 

1. A webböngészőben lépjen az [Azure SDK letöltések és SDK-k](https://azure.microsoft.com/downloads/) lapra. 
2. A **Parancssori eszközök** -> **PowerShell** szakaszban kattintson a **Windows-telepítés** elemre. 
3. Futtassa az **MSI**-fájlt az Azure PowerShell telepítéséhez. 

Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Bejelentkezés a PowerShellbe

1. Indítsa el a gépén a **PowerShellt**. Hagyja megnyitva a PowerShell ablakát a rövid útmutató végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.

    ![A PowerShell indítása](media/tutorial-hybrid-copy-powershell/search-powershell.png)
1. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt Azure-beli felhasználói nevét és jelszavát:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Ha több Azure-előfizetéssel is rendelkezik, futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Ha több Azure-előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le a **SubscriptionId** kifejezést az Azure-előfizetés azonosítójára:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.
3. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az adat-előállító neveként használhat. A névnek betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. Például: ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```
1. Adjon meg egy változót az adat-előállító helyéhez: 

    ```powershell
    $location = "East US"
    ```  
5. Az adat-előállító létrehozásához futtassa az alábbi **Set-AzureRmDataFactoryV2** parancsmagot: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFv2TutorialDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

## <a name="create-a-self-hosted-ir"></a>Saját üzemeltetésű integrációs modul létrehozása

Ebben a szakaszban egy saját üzemeltetésű integrációs modult hozhat létre, és társíthatja azt az SQL Server-adatbázist futtató helyszíni géppel. A saját üzemeltetésű integrációs modul az a komponens, amely adatokat másol a gépen futó SQL Serverről az Azure Blob Storage-ba. 

1. Hozzon létre egy változót az integrációs modul nevéhez. Adjon meg egy egyedi nevet, és jegyezze fel. Az oktatóanyagban későbbi részében használni fogja. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Hozzon létre egy saját üzemeltetésű integrációs modult. 

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

3. Futtassa az alábbi parancsot a **hitelesítési kulcsok** lekéréséhez, hogy a saját üzemeltetésű integrációs modult regisztrálhassa a Data Factory-szolgáltatásban a felhőben. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Itt látható a minta kimenete:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. Másolja be az egyik kulcsot (idézőjelek nélkül) a saját üzemeltetésű integrációs modul regisztrálásához, amelyet a következő lépésben telepíteni fog a gépére.  

## <a name="install-integration-runtime"></a>Integrációs modul telepítése
1. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) a saját üzemeltetésű integrációs modult egy helyi windowsos gépre, és futtassa a telepítést. 
2. Az **Üdvözli a Microsoft Integrációs modul telepítővarázsló** oldalon kattintson a **Tovább** gombra.  
3. A **Végfelhasználói licencszerződés** oldalon fogadja el a felhasználási feltételeket és a licencszerződést, majd kattintson a **Tovább** gombra. 
4. A **Célmappa** lapon kattintson a **Tovább** gombra. 
5. **A Microsoft Integrációs modul telepítésre kész** oldalon kattintson a **Telepítés** elemre. 
6. Ha figyelmeztető üzenet jelenik meg azzal kapcsolatban, hogy a számítógép használaton kívül automatikus alvó módba lépésre vagy hibernálásra lesz konfigurálva, kattintson az **OK** gombra. 
7. Ha megjelenik az **Energiagazdálkodási lehetőségek** ablak, zárja be, és váltson a telepítési ablakra. 
8. **A Microsoft Integrációs modul telepítővarázslójának befejezése** lapon kattintson a **Befejezés** gombra.
9. Az **Integrációs modul regisztrálása (Saját üzemeltetésű)** lapon illessze be az előző szakaszban mentett kulcsot, és kattintson a **Regisztráció** gombra. 

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
7. Teszteltje az SQL Server-adatbázissal létesített kapcsolatot.

    ![Diagnosztika lap](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    - A **Configuration Manager** ablakban váltson a **Diagnosztika** lapra.
    - Válassza az **SqlServer** értéket az **Adatforrás típusa** számára.
    - Adja meg a **kiszolgáló** nevét.
    - Adja meg az **adatbázis** nevét. 
    - Válassza ki a **hitelesítési** módot. 
    - Adja meg a **felhasználó** nevét. 
    - Adja meg a felhasználónévhez tartozó **jelszót**.
    - Kattintson a **Teszt** gombra, és ellenőrizze, hogy az integrációs modul kapcsolódik-e az SQL Serverhez. Sikeres kapcsolódás esetén egy zöld pipa jelenik meg. Sikertelen csatlakozás esetén a hibához kapcsolódó hibaüzenet jelenik meg. Javítsa ki a hibákat, és ellenőrizze, hogy az integrációs modul kapcsolódik-e az SQL Serverhez.
    - Jegyezze fel ezeket az értékeket (hitelesítési típus, kiszolgáló, adatbázis, felhasználó, jelszó). Az oktatóanyag részében használni fogja őket. 
    
      
## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hozhat létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóhoz történő társításához. Ebben az oktatóanyagban társítani fogja az Azure Storage-fiókot és a helyszíni SQL Servert az adattárhoz. A társított szolgáltatások azon kapcsolatadatokkal rendelkeznek, amelyeket a Data Factory szolgáltatás használ futtatáskor a hozzájuk való kapcsolódáshoz. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage-beli társított szolgáltatás (cél/fogadó) létrehozása
Ebben a lépésben az Azure Storage-fiókot társítja az adat-előállítóval.

1. Hozzon létre egy **AzureStorageLinkedService.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában az alábbi tartalommal: Ha még nem létezne, hozza létre az ADFv2Tutorial nevű mappát.  

    > [!IMPORTANT]
    > A fájl mentése előtt az &lt;accountName&gt; és az &lt;accountKey&gt; kifejezést cserélje le az **Azure Storage-fiók** nevére és kulcsára. Az [előfeltételek](#get-storage-account-name-and-account-key) részeként már feljegyezte ezeket.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

    Ha a Jegyzettömböt használja, a **Mentés másként** párbeszédpanel **Fájltípus** mezőjénél válassza a **Minden fájl** lehetőséget. Ellenkező esetben a rendszer `.txt` kiterjesztést adhat a fájlhoz. Például: `AzureStorageLinkedService.json.txt`. Ha létrehozza a fájlt a Fájlkezelőben, mielőtt megnyitná a Jegyzettömbben, előfordulhat, hogy nem fogja látni a hozzáadott `.txt` kiterjesztést, mivel alapértelmezés szerint engedélyezve van az **Ismert fájltípusok kiterjesztéseinek elrejtése** beállítás. A továbblépés előtt távolítsa el a hozzáadott `.txt` kiterjesztést. 
2. Az **Azure PowerShellben** váltson a **C:\ADFv2Tutorial** mappára.

   Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot az **AzureStorageLinkedService** társított szolgáltatás létrehozásához. 

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

    Ha „fájl nem található” hiba jelenik meg. A `dir` parancs futtatásával ellenőrizheti, hogy a fájl létezik-e. Ha a fájlnévhez tartozó kiterjesztés `.txt` (például AzureStorageLinkedService.json.txt), távolítsa el, majd futtassa ismét a PowerShell-parancsot. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server-alapú társított szolgáltatás létrehozása és titkosítása (forrás)
Ebben a lépésben a helyszíni SQL Servert társítja az adat-előállítóval.

1. Hozzon létre egy **SqlServerLinkedService.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal: Válassza ki az SQL Serverhez való kapcsolódáshoz használt **hitelesítési** módszernek megfelelő szakaszt.  

    > [!IMPORTANT]
    > Válassza ki az SQL Serverhez való kapcsolódáshoz használt **hitelesítési** módszernek megfelelő szakaszt.

    **SQL-hitelesítés (sa) használata esetén másolja a következő JSON-definíciót:**

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
    **Windows-hitelesítés használata esetén másolja a következő JSON-definíciót:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
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
    > [!IMPORTANT]
    > - Válassza ki az SQL Serverhez való kapcsolódáshoz használt **hitelesítési** módszernek megfelelő szakaszt.
    > - Cserélje le az **&lt;integration** **runtime** **name>** értéket az integrációs modul nevére.
    > - A fájl mentése előtt a **&lt;servername>**, **&lt;databasename>**, **&lt;username>** és **&lt;password>** értékeket cserélje le az SQL Server értékeire.
    > - Ha perjel karaktert (`\`) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja az escape-karaktert (`\`). Például: `mydomain\\myuser`. 

2. A bizalmas adatok (felhasználónév, jelszó stb.) titkosításához futtassa a **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** parancsmagot. A titkosítás a hitelesítő adatokat az adatvédelmi API (DPAPI) segítségével titkosítja. A titkosított hitelesítő adatok tárolása a saját üzemeltetésű integrációs modul csomópontján helyileg történik (a helyi gépen). A kimenő hasznos adatok átirányíthatóak egy másik JSON-fájlba (ebben az esetben az „encryptedLinkedService.json” fájlba), amely titkosított hitelesítő adatokat tartalmaz.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Futtassa az alábbi parancsot, amely létrehozza az **EncryptedSqlServerLinkedService** fájlt:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben olyan bemeneti és kimeneti adatkészleteket hoz létre, amelyek a másolási művelet be- és kimeneti adatait képezik majd (Helyszíni SQL Server-adatbázis => Azure Blob Storage).

### <a name="create-a-dataset-for-source-sql-database"></a>Adatkészlet létrehozása a forrás SQL-adatbázishoz
Ebben a lépésben megadhat egy adatkészletet, amely az SQL Server-adatbázisban tárolt adatokat jelöli. Az adatkészlet típusa **SqlServerTable**. Ez az előző lépésben létrehozott **SQL Server társított szolgáltatásra** vonatkozik. A társított szolgáltatás azon **kapcsolatadatokkal** rendelkezik, amelyeket a Data Factory szolgáltatás használ futtatáskor az SQL Serverhez való kapcsolódásra. Ez az adatkészlet határozza meg az **SQL-táblát** az adatokat tartalmazó adatbázisban. Ebben az oktatóanyagban a forrásadatokat az `emp` tábla tartalmazza. 

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
Ebben a lépésben az Azure Blob Storage-be másolni kívánt adatokat képviselő adatkészletet adja meg. Az adatkészlet típusa **AzureBlob**. Ez az oktatóanyagban korábban létrehozott **Azure Storage társított szolgáltatásra** vonatkozik. A társított szolgáltatás azon **kapcsolatadatokkal** rendelkezik, amelyeket a Data Factory szolgáltatás használ futtatáskor az Azure Storage-fiókhoz való kapcsolódásra. Ez az **adatkészlet** azt az Azure Storage-beli **mappát** határozza meg, amelybe a rendszer az adatokat másolja az SQL Server-adatbázisból. Ebben az oktatóanyagban a mappa a következő: `adftutorial/fromonprem`, ahol az `adftutorial` a blobtároló, a `fromonprem` pedig a mappa. 

1. Hozzon létre egy **AzureBlobDataset.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal:

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

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben az oktatóanyagban létre fog hozni egy másolási tevékenységgel rendelkező folyamatot. A másolási tevékenység az **SqlServerDataset** készletet használja bemeneti adatkészletként, illetve az **AzureBlobDataset** készletet kimeneti adatkészletként. A forrás típusa **SqlSource**, a fogadó típusa **BlobSink**.

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


## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása
Indítsa el az „SQLServerToBlobPipeline” folyamat futását, és őrizze meg a folyamat futtatási azonosítóját későbbi monitorozás céljából.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Az **SQLServerToBlobPipeline** folyamat futási állapotának folyamatos ellenőrzéséhez futtassa a következő szkriptet, majd nyomtassa ki a végső eredményt. Másolja/illessze be az alábbi szkriptet a PowerShell-ablakba, majd nyomja le az Enter billentyűt.

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

3. Az **SQLServerToBlobPipeline** folyamat futtatási azonosítójának lekéréséhez és a tevékenységfuttatás részletes eredményének áttekintéséhez futtassa az alábbi parancsot: 

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
A folyamat automatikusan létrehozza a `fromonprem` nevű kimeneti mappát az `adftutorial` blobtárolóban. Győződjön meg arról, hogy a **dbo.emp.txt** fájl megjelenik a kimeneti mappában. 

1. Az Azure Portal **adftutorial** tároló lapján kattintson a **Frissítés** elemre a kimeneti mappa megtekintéséhez.

    ![kimeneti mappa létrehozva](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. A mappák listájában kattintson a `fromonprem` elemre. 
3. Ellenőrizze, hogy megjelenik-e a `dbo.emp.txt` nevű fájl.

    ![kimeneti fájl](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


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
