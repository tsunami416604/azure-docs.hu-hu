---
title: Adatbázisok biztonsági mentése és visszaállítása – Azure SQL Edge (előzetes verzió)
description: Tudnivalók a biztonsági mentési és visszaállítási képességekről az Azure SQL Edge-ben (előzetes verzió)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5c7bdbc49d8f1c6af7f38911919c660b03e1a37a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596952"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Adatbázisok biztonsági mentése és visszaállítása az Azure SQL Edge-ben (előzetes verzió) 

Az Azure SQL Edge a Microsoft SQL Server adatbázismotor Linux rendszeren elérhető legújabb verziójára épül, amely hasonló biztonsági mentési és visszaállítási adatbázis-képességeket biztosít, mint a SQL Server on Linux és a tárolókban futó SQL Server. A biztonsági mentési és visszaállítási összetevő alapvető védelmet biztosít az Azure SQL Edge-adatbázisokban tárolt adatok védelméhez. A katasztrofális adatvesztés kockázatának csökkentése érdekében ajánlott rendszeresen biztonsági másolatot készíteni az adatbázisokról, hogy rendszeresen megőrizze az adataiban történt módosításokat. Egy jól megtervezett biztonsági mentési és visszaállítási stratégia segít megvédeni az adatbázisokat a különböző hibák által okozott adatvesztéstől. A stratégia leteszteléséhez állítson vissza egy sor biztonsági mentést, majd állítsa helyre az adatbázist, hogy vészhelyzetben felkészülten és hatékonyan reagálhasson.

Ha többet szeretne megtudni arról, hogy a biztonsági mentések Miért fontosak, tekintse meg [a SQL Server adatbázisok biztonsági mentése és visszaállítása](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)című témakört.

Az Azure SQL Edge a helyi tárolóból vagy az Azure-blobokból történő biztonsági mentést és visszaállítást is támogatja. Az Azure Blob Storageról történő biztonsági mentéssel és visszaállítással kapcsolatos további információkért tekintse meg [SQL Server biztonsági mentést és visszaállítást Microsoft Azure Blob Storage szolgáltatással](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) , valamint [SQL Server biztonsági mentést az URL-címre](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Adatbázis biztonsági mentése az Azure SQL Edge-ben

Az Azure SQL Edge a SQL Server által támogatott biztonsági mentési típusokat támogatja. A SQL Server által támogatott biztonsági mentési típusok teljes listájáért tekintse meg a [biztonsági mentés áttekintése című témakört](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Az Azure SQL Edge-ben létrehozott adatbázisok alapértelmezés szerint egyszerű helyreállítási modellt használnak. Ilyen naplók biztonsági mentése nem hajtható végre ezeken az adatbázisokon. Ha szükség van a naplók biztonsági mentésére ezen adatbázisokon, a rendszergazdának módosítania kell az adatbázis-helyreállítási modellt teljes helyreállítási modellre. A SQL Server által támogatott helyreállítási modellek teljes listáját a [helyreállítási modell áttekintése című témakörben](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)találja.

### <a name="backup-to-local-disk"></a>Biztonsági mentés helyi lemezre

Az alábbi példában a BACKUP DATABASE Transact-SQL parancs használatával lehet létrehozni egy adatbázis biztonsági másolatát a tárolóban. Ebben a példában egy "Backup" nevű új mappa jön létre a biztonságimásolat-fájlok tárolására.

1. Hozzon létre egy mappát a biztonsági mentésekhez. Ezt a parancsot az Azure SQL Edge-tárolót futtató gazdagépen kell végrehajtani. Az alábbi parancsban cserélje le **<AzureSQLEdge_Container_Name>** az Azure SQL Edge-tároló nevére a telepítésben.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Kapcsolódjon az Azure SQL Edge-példányhoz SQL Server Management Studio (SSMS) vagy Azure Data Studio (ADS) használatával, és futtassa a Backup Database parancsot a felhasználói adatbázis biztonsági mentésének elvégzéséhez. Az alábbi példában a *IronOreSilicaPrediction* -adatbázis biztonsági mentését vesszük figyelembe.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Miután futtatta a parancsot, és az adatbázis biztonsági mentése sikeres volt, a SSMS vagy a hirdetések eredmények szakaszában a következőhöz hasonló üzenetek jelennek meg.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>Biztonsági mentés az URL-címre

Az Azure SQL Edge a blobok és a Blobok blokkolására is támogatja a biztonsági mentést. A blobok és a Blobok blokkolásával kapcsolatos további információkért tekintse meg a következő témakört: a [blob vs oldal blobjának letiltása](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob). Az alábbi példában az adatbázis- *IronOreSilicaPrediction* biztonsági mentése egy blokk-blobba történik. 

1. A biztonsági mentések a Blobok számára történő konfigurálásának első lépéseként létre kell hozni egy közös hozzáférésű aláírási (SAS) tokent, amely SQL Server hitelesítő adatok létrehozásához használható az Azure SQL Edge-ben. A parancsfájl létrehoz egy megosztott elérési aláírást, amely egy tárolt hozzáférési szabályzathoz van társítva. További információt [a közös hozzáférésű aláírások, 1. rész: az SAS-modell megismerése című részben](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)talál. A parancsfájl a T-SQL-parancsot is írja a hitelesítő adatok SQL Server való létrehozásához. Az alábbi szkript feltételezi, hogy már rendelkezik egy Storage-fiókkal rendelkező Azure-előfizetéssel és egy tárolóval a biztonsági mentésekhez.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    A szkript sikeres futtatása után másolja a HITELESÍTő adatok létrehozása parancsot egy lekérdezési eszközre, kapcsolódjon a SQL Server egy példányához, és futtassa a parancsot, hogy létrehozza a hitelesítő adatokat a közös hozzáférési aláírással.

2. Kapcsolódjon az Azure SQL Edge-példányhoz SQL Server Management Studio (SSMS) vagy Azure Data Studio (ADS) használatával, és hozza létre a hitelesítő adatokat az előző lépésben megadott paranccsal. Ügyeljen arra, hogy a HITELESÍTő adat létrehozása parancsot az előző lépésből származó tényleges kimenetre cserélje.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. A következő parancs a *IronOreSilicaPrediction* biztonsági mentését végzi az Azure Storage-tárolóban.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>Adatbázis visszaállítása az Azure SQL Edge-ben

Az Azure SQL Edge támogatja a helyi lemezről, egy hálózati helyről vagy egy Azure Blob Storage-fiókból történő visszaállítást. A SQL Server visszaállításának és helyreállításának áttekintéséhez tekintse át a [visszaállítás és helyreállítás áttekintése](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15)című témakört. A SQL Server egyszerű helyreállítási modelljének áttekintéséhez tekintse át az adatbázis-visszaállítások teljes visszaállítását ismertető cikket [(egyszerű helyreállítási modell)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-local-disk"></a>Visszaállítás helyi lemezről

Ez a példa az előző példában szereplő *IronOreSilicaPrediction* biztonsági mentést használja új adatbázisként, más néven.

1. Ha az adatbázis biztonságimásolat-fájlja még nem szerepel a tárolóban, az alábbi parancs használatával másolhatja a fájlt a tárolóba. Az alábbi példa azt feltételezi, hogy a biztonságimásolat-fájl megtalálható a helyi gazdagépen, és a rendszer átmásolja a/var/opt/MSSQL/Backup mappába egy sql1 nevű Azure SQL Edge-tárolóba.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Kapcsolódjon az Azure SQL Edge-példányhoz SQL Server Management Studio (SSMS) vagy Azure Data Studio (ADS) használatával a Restore parancs végrehajtásához. Az alábbi példában a **IronOrePredictDB. bak** új adatbázis létrehozására lett visszaállítva **IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. A Restore parancs futtatása után, ha a visszaállítási művelet sikeres volt, a kimenet ablakban a következőhöz hasonló üzenetek jelennek meg. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Visszaállítás az URL-címről

Az Azure SQL Edge támogatja az adatbázisok Azure Storage-fiókból való visszaállítását is. A visszaállításokat a Blobok vagy a Blobok blobos biztonsági másolatai is elvégezhetik. Az alábbi példában a *IronOreSilicaPrediction_2020_04_16. bak* adatbázis biztonságimásolat-fájlját a rendszer visszaállítja az adatbázis- *IronOreSilicaPrediction_3*létrehozásához.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


