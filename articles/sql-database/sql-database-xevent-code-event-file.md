---
title: XEvent eseményfájl kódja
description: PowerShell és Transact-SQL egy kétfázisú kódminta, amely bemutatja az Event File cél egy kiterjesztett esemény az Azure SQL Database.Provides PowerShell and Transact-SQL for a two-phase code sample that demonstrates the Event File target in an extended event on Azure SQL Database. Az Azure Storage a forgatókönyv szükséges része.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 03/12/2019
ms.openlocfilehash: a9bf28fb1b3c5278d25b417fc646d2ad3d6f1abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213990"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Eseményfájl célkódja az SQL Database kiterjesztett eseményeihez

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Teljes kódmintát szeretne a kiterjesztett események adatainak rögzítésére és jelentésére szolgáló robusztus módszerhez.

A Microsoft SQL Server rendszerben az [Eseményfájl-tároló](https://msdn.microsoft.com/library/ff878115.aspx) az eseménykimenetek helyi merevlemez-fájlban való tárolására szolgál. De az ilyen fájlok nem érhetők el az Azure SQL Database számára. Ehelyett az Azure Storage szolgáltatást használjuk az eseményfájl-cél támogatásához.

Ez a témakör kétfázisú kódmintát mutat be:

- PowerShell, hozzon létre egy Azure Storage-tárolót a felhőben.
- Transact-SQL:
  
  - Az Azure Storage-tároló hozzárendelése egy eseményfájl-tárolóhoz.
  - Az eseménymunkamenet létrehozása és elindítása, és így tovább.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

- Azure-fiók és -előfizetés. Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
- Bármely adatbázis, amelyben táblát hozhat létre.
  
  - Tetszés szerint percek [alatt létrehozhat egy **AdventureWorksLT** bemutató adatbázist.](sql-database-get-started.md)

- SQL Server Management Studio (ssms.exe), ideális esetben a legújabb havi frissítési verzió.
  A legújabb ssms.exe fájl letölthető a következő oldalról:
  
  - Az [SQL Server Management Studio letöltése](https://msdn.microsoft.com/library/mt238290.aspx)című témakörben.
  - [Közvetlen link a letöltéshez.](https://go.microsoft.com/fwlink/?linkid=616025)

- Az Azure [PowerShell-modulok telepítve](https://go.microsoft.com/?linkid=9811175) kell lennie.

  - A modulok olyan parancsokat biztosítanak, mint a - **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>1. fázis: PowerShell-kód az Azure Storage-tárolóhoz

Ez a PowerShell a kétfázisú kódminta 1.

A parancsfájl egy lehetséges előző futtatás után megtisztítandó parancsokkal kezdődik, és újrafuttatható.

1. Illessze be a PowerShell-parancsfájlt egy egyszerű szövegszerkesztőbe, például a Notepad.exe fájlba, és mentse a parancsfájlt **.ps1**kiterjesztésű fájlként.
2. Indítsa el a PowerShell ISE-t rendszergazdaként.
3. A parancssorba írja be a<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>majd nyomja le az Enter billentyűt.
4. A PowerShell ISE-ben nyissa meg a **.ps1** fájlt. Futtassa a szkriptet.
5. A parancsfájl először egy új ablakot indít el, amelyben bejelentkezik az Azure-ba.

   - Ha a munkamenet megszakítása nélkül futtatja újra a parancsfájlt, kényelmesen eltudja mondani az **Add-AzureAccount** parancsot.

![PowerShell ISE, az Azure modul telepítve, készen áll a parancsfájl futtatására.][30_powershell_ise]

### <a name="powershell-code"></a>PowerShell-kód

Ez a PowerShell-parancsfájl feltételezi, hogy már telepítette az Az modult. További információt [az Azure PowerShell-modul telepítése című témakörben](/powershell/azure/install-Az-ps)talál.

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Vegye figyelembe a néhány elnevezett értékeket, amelyeket a PowerShell-parancsfájl nyomtat, amikor véget ér. Ezeket az értékeket a Transact-SQL parancsfájlba kell írnia, amely a második fázisban következik.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>2. fázis: Az Azure Storage-tárolót használó Transact-SQL-kód

- A kódminta 1.
- A következő fázisban a következő Transact-SQL parancsfájlnak kell használnia a tárolót.

A parancsfájl egy lehetséges előző futtatás után megtisztítandó parancsokkal kezdődik, és újrafuttatható.

A PowerShell-parancsfájl néhány elnevezett értéket nyomtatott, amikor véget ért. Az értékek használatához a Transact-SQL parancsfájlt kell szerkesztenie. Keresse meg a **TODO-t** a Transact-SQL parancsfájlban a szerkesztési pontok megkereséséhez.

1. Nyissa meg az SQL Server Management Studio (ssms.exe) programot.
2. Csatlakozzon az Azure SQL Database-adatbázishoz.
3. Ide kattintva új lekérdezési ablaktáblát nyithat meg.
4. Illessze be a következő Transact-SQL parancsfájlt a lekérdezési ablaktáblába.
5. Keresse meg az összes **TODO** a forgatókönyvet, és a megfelelő módosításokat.
6. Mentse, majd futtassa a parancsfájlt.

> [!WARNING]
> Az előző PowerShell-parancsfájl által létrehozott SAS-kulcsérték "?" (kérdőjel). Ha a sas-kulcsot a következő T-SQL parancsfájlban használja, el kell *távolítania a "?* Ellenkező esetben a biztonsági akta blokkolhatja az erőfeszítéseit.

### <a name="transact-sql-code"></a>Transact-SQL-kód

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Ha a cél futtatásakor nem sikerül csatolni, le kell állítania és újra kell indítania az eseménymunkamenetet:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Kimenet

Amikor a Transact-SQL parancsfájl befejeződik, kattintson egy cellára a **event_data_XML** oszlopfejléc alatt. Egy ** \<esemény>** elem jelenik meg, amely egy UPDATE utasítást jelenít meg.

Itt van ** \<** egy esemény>elem, amely a tesztelés során keletkezett:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

Az előző Transact-SQL parancsfájl a következő rendszerfüggvényt használta a event_file olvasásához:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

A bővített eseményekből származó adatok megtekintésére vonatkozó speciális lehetőségek magyarázata a következő helyen érhető el:

- [A céladatok speciális megtekintése kiterjesztett eseményekből](https://msdn.microsoft.com/library/mt752502.aspx)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>A kódminta konvertálása SQL Server kiszolgálón való futtatásra

Tegyük fel, hogy az előző Transact-SQL mintát a Microsoft SQL Server rendszeren szeretné futtatni.

- Az egyszerűség kedvéért teljesen le kell cserélnie az Azure Storage-tároló használatát egy egyszerű fájlra, például *a C:\myeventdata.xel fájlra.* A fájl az SQL Server rendszert tároló számítógép helyi merevlemezére lesz írva.
- Nem kell semmilyen Transact-SQL utasítás a **CREATE MASTER KEY** és create **credential**.
- A **CREATE EVENT SESSION** utasítás **ADD TARGET** záradékában a **fájlnévhez** rendelt Http-értéket egy teljes elérési karakterláncra cserélné, például *C:\myfile.xel.*
  
  - Nem Azure Storage-fiók kell vonni.

## <a name="more-information"></a>További információ

Az Azure Storage-szolgáltatás fiókjairól és tárolóiról a következő témakörben talál további információt:

- [A Blob storage használata a .NET-ről](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [Tárolók, blobok és metaadatok elnevezése és hivatkozása](https://msdn.microsoft.com/library/azure/dd135715.aspx)
- [A gyökértároló val való együttműködés](https://msdn.microsoft.com/library/azure/ee395424.aspx)
- [1. lecke: Tárolt hozzáférési szabályzat és megosztott hozzáférés-aláírás létrehozása egy Azure-tárolóban](https://msdn.microsoft.com/library/dn466430.aspx)
  - [2. lecke: SQL Server-hitelesítő adatok létrehozása megosztott hozzáférésű aláírás használatával](https://msdn.microsoft.com/library/dn466435.aspx)
- [Kiterjesztett események a Microsoft SQL Server rendszerhez](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!-- Image references. -->
[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png
