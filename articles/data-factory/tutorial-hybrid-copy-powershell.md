---
title: Adatok másolása az SQL Server-adatbázisból Blob Storage-tárolóba az Azure Data Factory használatával | Microsoft Docs
description: Megismerheti az adatok a helyszíni adattárolókból az Azure-beli felhőbe, az Azure Data Factory saját üzemeltetésű Integration Runtime átjáróval történő másolásának folyamatát.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: ea5e393ebe204041d96d18481a5c64d2877755f2
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304611"
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Oktatóanyag: Adatok másolása helyszíni SQL Server-adatbázisból Azure Blob Storage-tárolóba
Ebben az oktatóanyagban az Azure PowerShell használatával egy Data Factory-folyamatot hozunk létre az adatok egy helyszíni SQL Server-adatbázisból egy Azure Blob-tárolóba történő másolására. Létrehozhat és alkalmazhat egy saját üzemeltetésű integrációs modult, amely adatokat helyez át a helyszíni és a felhőalapú adattárolók között. 

> [!NOTE]
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. További információkért lásd: [Az Azure Data Factory bemutatása](introduction.md). 

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
Ha még nem rendelkezik Azure-előfizetéssel, első lépésként [hozzon létre egy ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="azure-roles"></a>Azure-szerepkörök
Az adat-előállító példányok létrehozásához annak a felhasználói fióknak, amellyel belép az Azure-ba, a *Közreműködő* vagy *Tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. 

Az előfizetésben található engedélyek megtekintéséhez kattintson az Azure Portalon a felhasználónevére a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. Ha szeretne példautasításokat látni egy felhasználó szerepkörhöz adására, olvassa el a [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md) című cikket.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 és 2017
Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk *forrásadattárként*. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol egy helyszíni SQL Server-adatbázisból (forrás) egy Azure Blob Storage-tárolóba (fogadó). Ezután létre fog hozni egy **emp** nevű táblát az SQL Server-adatbázisban, és beszúr a táblába néhány mintabejegyzést. 

1. Indítsa el az SQL Server Management Studiót. Ha még nincs telepítve a számítógépen, tekintse meg az [Az SQL Server Management Studio letöltését ismertető](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) szakaszt. 

1. Csatlakozzon az SQL Server-példányához a hitelesítő adataival. 

1. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal a **Databases** (Adatbázisok) elemre, majd válassza a **New Database** (Új adatbázis) elemet. 
 
1. Az **New Database** (Új adatbázis) ablakban adjon nevet az új adatbázisnak, majd kattintson az **OK** gombra. 

1. Az **emp** tábla létrehozásához és néhány mintaadat beszúrásához futtassa a következő lekérdezési szkriptet az adatbázison:

   ```
       INSERT INTO emp VALUES ('John', 'Doe')
       INSERT INTO emp VALUES ('Jane', 'Doe')
       GO
   ```

1. A fanézetben kattintson a jobb gombbal a létrehozott adatbázisra, majd válassza a **New Query** (Új lekérdezés) elemet.

### <a name="azure-storage-account"></a>Azure Storage-fiók
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben Azure Blob Storage-tárolót) használunk cél-/fogadóadattárként. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikket. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol a helyszíni SQL Server-adatbázisból (forrás) az Azure Blob Storage tárolóba (fogadó). 

#### <a name="get-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben az oktatóanyagban az Azure Storage-fiók nevét és kulcsát használjuk. A tárfiók nevét és kulcsát az alábbi módon szerezheti be: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure felhasználónevével és jelszavával. 

1. A bal oldali ablaktáblán válassza ki a **További szolgáltatások** lehetőséget, szűrjön rá a **Tárolás** kulcsszóra, majd válassza ki a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. A tárfiókok listájában állítson be szűrőt a tárfiók nevéhez (ha szükséges), majd válassza ki a tárfiókját. 

1. A **Tárfiók** ablakban válassza a **Hozzáférési kulcsok** elemet.

    ![Tárfióknév és -kulcs beszerzése](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

1. Másolja a **Tárfiók neve** és **1. kulcs** mező értékét, majd illessze be őket egy jegyzettömbbe vagy más szerkesztőbe az oktatóanyag későbbi részeiben történő használatra. 

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása 
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre az Azure Blob Storage-ban. 

1. A **Tárfiók** ablakban váltson át az **Áttekintés** panelre, majd válassza a **Blobok** elemet. 

    ![A Blobok elem választása](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. A **Blob service** ablakban válassza a **Tároló** elemet. 

    ![Tároló hozzáadása gomb](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. Az **Új tároló** ablak **Név** mezőjébe írja be az **adftutorial** nevet, majd kattintson az **OK** gombra. 

    ![Tárolónév megadása](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. A tárolók listájában kattintson az **adftutorial** elemre.  

    ![A tároló kiválasztása](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

1. Ne zárja be az **adftutorial** **tároló** ablakát. A segítségével ellenőrizheti az oktatóanyag eredményét. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia.

    ![Tároló ablaka](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése
Ha még nincs a gépén, telepítse az Azure PowerShell legújabb verzióját. 

1. Lépjen az [Azure SDK letöltések](https://azure.microsoft.com/downloads/) lapjára. 

1. A **Parancssori eszközök** terület **PowerShell** szakaszában kattintson a **Telepítés Windows rendszeren** elemre. 

1. Futtassa az MSI-fájlt az Azure PowerShell telepítéséhez. 

Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Bejelentkezés a PowerShellbe

1. Indítsa el a PowerShellt a számítógépen, és ne zárja be a gyors üzembe helyezési oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.

    ![A PowerShell elindítása](media/tutorial-hybrid-copy-powershell/search-powershell.png)

1. Futtassa a következő parancsot, majd adja meg az Azure Portalra való bejelentkezéshez használt Azure-beli felhasználói nevét és jelszavát:
       
    ```powershell
    Connect-AzureRmAccount
    ```        

1. Ha több Azure-előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le a **SubscriptionId** kifejezést az Azure-előfizetés azonosítójára:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét (idézőjelek között, például így: `"adfrg"`), majd futtassa a parancsot. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az adat-előállító neveként használhat. A névnek betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét egy globálisan egyedi névvel. Például: ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. Adjon meg egy változót az adat-előállító helyéhez: 

    ```powershell
    $location = "East US"
    ```  

1. Az adat-előállító létrehozásához futtassa az alábbi `Set-AzureRmDataFactoryV2`-parancsmagot: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * Az adat-előállító nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Az adat-előállító példányok létrehozásához annak a felhasználói fióknak, amellyel belép az Azure-ba, a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie.
> * Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (Azure HDInsight stb.) más régiókban is lehetnek.
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime létrehozása

Ebben a szakaszban egy saját üzemeltetésű Integration Runtime átjárót hozhat létre, és társíthatja azt az SQL Server-adatbázist futtató helyszíni géppel. A saját üzemeltetésű Integration Runtime az a komponens, amely adatokat másol a gépen futó SQL Server-adatbázisból az Azure Blob Storage-ba. 

1. Hozzon létre egy változót az integrációs modul nevéhez. Adjon meg egy egyedi nevet, és jegyezze fel. Az oktatóanyag későbbi részében használni fogja. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. Hozzon létre egy saját üzemeltetésű integrációs modult. 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ``` 
    Itt látható a minta kimenete:

    ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       : selfhosted IR description
    ```

1. Futtassa az alábbi parancsot a létrehozott Integration Runtime állapotának lekéréséhez:

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

1. Futtassa az alábbi parancsot a *hitelesítési kulcsok* lekéréséhez, hogy a saját üzemeltetésű Integration Runtime átjárót regisztrálhassa a Data Factory-szolgáltatásban a felhőben. Másolja be az egyik kulcsot (idézőjelek nélkül) a saját üzemeltetésű Integration Runtime regisztrálásához, amelyet a következő lépésben telepíteni fog a gépére. 

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

## <a name="install-the-integration-runtime"></a>Az Integration Runtime telepítése
1. Töltse le az [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) modult egy helyi Windows-gépre, és futtassa a telepítést. 

1. A **Microsoft Integration Runtime telepítővarázslójának üdvözlő oldalán** kattintson a **Tovább** gombra.  

1. A **Végfelhasználói licencszerződés** ablakban fogadja el a felhasználási feltételeket és a licencszerződést, majd válassza a **Tovább** lehetőséget. 

1. A **Célmappa** ablakban válassza a **Tovább** lehetőséget. 

1. A **Microsoft Integration Runtime telepítésre kész** ablakban kattintson a **Telepítés** gombra. 

1. Ha figyelmeztető üzenet jelenik meg azzal kapcsolatban, hogy a számítógép használaton kívül automatikus alvó módba lépésre vagy hibernálásra lesz konfigurálva, kattintson az **OK** gombra. 

1. Ha megjelenik az **Energiagazdálkodási lehetőségek** ablak, zárja be, és váltson a telepítési ablakra. 

1. **A Microsoft Integration Runtime telepítővarázslójának befejező lapján** kattintson a **Befejezés** gombra.

1. Az **Integration Runtime (helyi) regisztrálása** ablakban illessze be az előző szakaszban mentett kulcsot, és kattintson a **Regisztráció** gombra. 

    ![Integrációs modul regisztrálása](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

    A helyi Integration Runtime sikeres regisztrációja esetén a következő üzenet jelenik meg: 

    ![Sikeres regisztráció](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. Az **Integration Runtime (helyi) új csomópontja** ablakban kattintson a **Tovább** gombra. 

    ![Integration Runtime új csomópontja ablak](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

1. Az **Intranetes kommunikációs csatorna** ablakban kattintson a **Kihagyás** gombra.  
    Választhat TLS/SSL-hitelesítést a csomóponton belüli kommunikációhoz többcsomópontos integrációs modul környezetben.

    ![Intranetes kommunikációs csatorna ablak](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)

1. Az **Integration Runtime (helyi) regisztrálása** ablakban kattintson **A Configuration Manager indítása** gombra. 

1. A csomópont a felhőszolgáltatáshoz való csatlakozásakor a következő üzenet jelenik meg:

    ![Csomópont csatlakoztatva](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. Teszteltje az SQL Server-adatbázissal létesített kapcsolatot az alábbi módon:

    ![Diagnosztika lap](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    a. A **Configuration Manager** ablakban váltson a **Diagnosztika** lapra.

    b. Az **Adatforrás típusa** mezőben válassza az **SqlServer** lehetőséget.

    c. Adja meg a kiszolgáló nevét.

    d. Adja meg az adatbázis nevét. 

    e. Válassza ki a hitelesítési módot. 

    f. Adja meg a felhasználónevet. 

    g. Adja meg a felhasználónévhez tartozó jelszót.

    h. Annak ellenőrzéséhez, hogy az Integration Runtime kapcsolódik-e az SQL Serverhez, kattintson a **Teszt** gombra.  
    Sikeres csatlakozás esetén egy zöld pipa jelenik meg. Sikertelen csatlakozás esetén a hibához kapcsolódó hibaüzenet jelenik meg. Javítsa ki a hibákat, és ellenőrizze, hogy az Integration Runtime kapcsolódik-e az SQL Server-példányhoz.

    Jegyezze fel az összes korábbi értéket, hogy az oktatóanyag további részében is felhasználhassa őket.
    
## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Adattárak és a számítási szolgáltatások adat-előállítóhoz történő társításához társított szolgáltatásokat kell létrehoznia az adat-előállítóban. Ebben az oktatóanyagban társítani fogja az Azure Storage-fiókot és a helyszíni SQL Server-példányt az adattárhoz. A társított szolgáltatások azon kapcsolatadatokkal rendelkeznek, amelyeket a Data Factory szolgáltatás használ futtatáskor a hozzájuk való kapcsolódáshoz. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage-beli társított szolgáltatás (cél/fogadó) létrehozása
Ebben a lépésben az Azure Storage-fiókot társítja az adat-előállítóval.

1. Hozzon létre egy *AzureStorageLinkedService.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kóddal. Ha még nem létezik *ADFv2Tutorial* nevű mappa, hozzon létre egyet.  

    > [!IMPORTANT]
    > A fájl mentése előtt az \<accountName> és az \<accountKey> kifejezést cserélje le az Azure Storage-fiókja nevére, illetve kulcsára. Ezeket az [Előfeltételek](#get-storage-account-name-and-account-key) szakaszban jegyezte fel.

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

1. A PowerShellben váltson a *C:\ADFv2Tutorial* mappára.

1. Futtassa a `Set-AzureRmDataFactoryV2LinkedService` parancsmagot az AzureStorageLinkedService társított szolgáltatás létrehozásához: 

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

    Ha „fájl nem található” hiba jelenik meg, a `dir` parancs futtatásával ellenőrizze, hogy a fájl létezik-e. Ha a fájlnévhez tartozó kiterjesztés *.txt* (például AzureStorageLinkedService.json.txt), távolítsa el, majd futtassa ismét a PowerShell-parancsot. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server-alapú társított szolgáltatás létrehozása és titkosítása (forrás)
Ebben a lépésben a helyszíni SQL Server-példányt társítja az adat-előállítóval.

1. Hozzon létre egy *SqlServerLinkedService.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kód használatával:

    > [!IMPORTANT]
    > Válassza ki az SQL Serverhez való kapcsolódáshoz használt hitelesítési módszernek megfelelő szakaszt.

    **SQL-hitelesítés használata (sa):**

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

    **Windows-hitelesítés használata:**

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
    > - Válassza ki az SQL Server-példányhoz való kapcsolódáshoz használt hitelesítési módszernek megfelelő szakaszt.
    > - Cserélje le az **\<integration runtime name>** értéket az Integration Runtime nevére.
    > - A fájl mentése előtt a **\<servername>**, **\<databasename>**, **\<username>** és **\<password>** értékeket cserélje le az SQL Server-példány értékeire.
    > - Ha fordított perjel karaktert (\\) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja előtte a feloldójelet (\\). Használja például a *sajáttartomány\\\\sajátfelhasználó* értéket. 

1. A bizalmas adatok (felhasználónév, jelszó stb.) titkosításához futtassa a `New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential` parancsmagot.  
    A titkosítás a hitelesítő adatokat az adatvédelmi API (DPAPI) segítségével titkosítja. A titkosított hitelesítő adatok tárolása a saját üzemeltetésű integrációs modul csomópontján helyileg történik (a helyi gépen). A kimenő hasznos adatok átirányíthatóak egy másik JSON-fájlba (ebben az esetben az *encryptedLinkedService.json* fájlba), amely titkosított hitelesítő adatokat tartalmaz.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. Futtassa az alábbi parancsot, amely létrehozza az EncryptedSqlServerLinkedService fájlt:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben bemeneti és kimeneti adatkészleteket hoz létre. Ezek a helyszíni SQL Server-adatbázisból az Azure Blob Storage-ba történő másolási művelet be- és kimeneti adatait képezik majd.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Adatkészlet létrehozása a forrás SQL Server-adatbázishoz
Ebben a lépésben megadhat egy adatkészletet, amely az SQL Server-adatbázispéldányban tárolt adatokat jelöli. Az adatkészlet típusa SqlServerTable. Ez az előző lépésben létrehozott SQL Server társított szolgáltatásra vonatkozik. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory szolgáltatás használ futtatáskor az SQL Server-példányhoz való kapcsolódásra. Ez az adatkészlet határozza meg az SQL-táblát az adatokat tartalmazó adatbázisban. Ebben az oktatóanyagban a forrásadatokat az **emp** tábla tartalmazza. 

1. Hozzon létre egy *SqlServerDataset.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kóddal:  

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

1. Az SqlServerDataset adatkészlet létrehozásához futtassa a `Set-AzureRmDataFactoryV2Dataset` parancsmagot.

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

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Adatkészlet létrehozása a Azure Blob Storage-hez (fogadó)
Ebben a lépésben az Azure Blob Storage-be másolni kívánt adatokat képviselő adatkészletet adja meg. Az adatkészlet típusa AzureBlob. Ez az oktatóanyagban korábban létrehozott Azure Storage társított szolgáltatásra vonatkozik. 

A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket az adat-előállító használ futtatáskor az Azure Storage-fiókhoz való kapcsolódásra. Ez az adatkészlet azt az Azure Storage-beli mappát határozza meg, amelybe a rendszer az adatokat másolja az SQL Server-adatbázisból. Ebben az oktatóanyagban a mappa a következő: *adftutorial/fromonprem*, ahol az `adftutorial` a blobtároló, a `fromonprem` pedig a mappa. 

1. Hozzon létre egy *AzureBlobDataset.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kóddal:

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

1. Az AzureBlobDataset adatkészlet létrehozásához futtassa a `Set-AzureRmDataFactoryV2Dataset` parancsmagot.

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
Ebben az oktatóanyagban létre fog hozni egy másolási tevékenységgel rendelkező folyamatot. A másolási tevékenység az SqlServerDataset készletet használja bemeneti adatkészletként, illetve az AzureBlobDataset készletet kimeneti adatkészletként. A forrás típusa *SqlSource*, a fogadó típusa *BlobSink*.

1. Hozzon létre egy *SqlServerToBlobPipeline.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kóddal:

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

1. Az SQLServerToBlobPipeline folyamat létrehozásához futtassa a `Set-AzureRmDataFactoryV2Pipeline` parancsmagot.

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
Indítsa el az SQLServerToBlobPipeline folyamat futását, és őrizze meg a folyamat futtatási azonosítóját későbbi monitorozás céljából.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Az SQLServerToBlobPipeline folyamat futási állapotának folyamatos ellenőrzéséhez futtassa a következő szkriptet PowerShellben, majd nyomtassa ki a végső eredményt:

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

1. Az SQLServerToBlobPipeline folyamat futtatási azonosítójának lekéréséhez és a tevékenységfuttatás részletes eredményének áttekintéséhez futtassa az alábbi parancsot: 

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
A folyamat automatikusan létrehozza a *fromonprem* nevű kimeneti mappát az `adftutorial` blobtárolóban. Győződjön meg arról, hogy a *dbo.emp.txt* fájl megjelenik a kimeneti mappában. 

1. Az Azure Portal **adftutorial** tároló ablakában kattintson a **Frissítés** elemre a kimeneti mappa megtekintéséhez.

    ![Kimeneti mappa létrehozva](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
1. A mappák listájában válassza a `fromonprem` elemet. 
1. Ellenőrizze, hogy megjelenik-e a `dbo.emp.txt` nevű fájl.

    ![Kimeneti fájl](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Azure Storage-beli társított szolgáltatások létrehozása. 
> * SQL Server- és Azure Blob-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

A Data Factory által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) cikk tartalmazza.

Folytassa a következő oktatóanyaggal, amelyben az adatok egy forrásból egy célhelyre történő tömeges másolását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok tömeges másolása](tutorial-bulk-copy.md)
