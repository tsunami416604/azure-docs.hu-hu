---
title: Az SQL Database XEvent Eseményfájl kód |} A Microsoft Docs
description: Biztosít egy kétfázisú kódminta azt mutatja be, az esemény Biztonságinaplófájl-cél Azure SQL Database kiterjesztett esemény a PowerShell és a Transact-SQL. Az Azure Storage ebben a forgatókönyvben egy szükséges részét képezi.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 61b3c01683318ff1c686ad9e96a420f8ae5e245e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306663"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Eseményfájl célkódja az SQL Database bővített események

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Szeretne egy hatékony módja egy kiterjesztett esemény kapcsolatos rögzítése és a jelentés egy teljes körű kódmintát.

A Microsoft SQL Server a [Eseményfájl cél](https://msdn.microsoft.com/library/ff878115.aspx) esemény a kimeneteket a helyi merevlemezre fájl tárolására szolgál. Azonban az ilyen fájlok nem érhetők el az Azure SQL Database. Az Azure Storage szolgáltatáshoz inkább az eseményfájl cél használható használjuk.

Ez a témakör egy kétfázisú kódmintát mutat be:

* PowerShell, a felhőben egy Azure Storage-tároló létrehozása.
* Transact-SQL:
  
  * Az Azure Storage-tároló hozzárendelése egy esemény Biztonságinaplófájl-cél.
  * Hozzon létre és indítsa el az esemény-munkamenet, és így tovább.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure-fiók és -előfizetés. Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Létrehozhat egy táblát a bármely olyan adatbázisába.
  
  * Igény szerint is [hozzon létre egy **AdventureWorksLT** mintaadatbázis](sql-database-get-started.md) percek alatt.
* Az SQL Server Management Studio (ssms.exe), ideális legújabb havi frissítése verzióját. 
  Letöltheti a legújabb ssms.exe származó:
  
  * Című témakör [SQL Server Management Studio letöltését](https://msdn.microsoft.com/library/mt238290.aspx).
  * [A letöltésére mutató közvetlen hivatkozást.](https://go.microsoft.com/fwlink/?linkid=616025)
* Rendelkeznie kell a [Azure PowerShell-modulok](https://go.microsoft.com/?linkid=9811175) telepítve.
  
  * A modulok parancsai például - tartalmaznak **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>1. fázis: PowerShell-kódot az Azure Storage-tárolók

A PowerShell az 1. fázisa az kétfázisú munkafüzetkód-példát.

A szkript után, egy korábbi lehet futtatni, rerunnable karbantartása parancsok kezdődik.

1. Illessze be a PowerShell-parancsfájl egy egyszerű szövegszerkesztőt, ilyen például a Notepad.exe, és mentse a parancsfájlt kiterjesztésű fájlként **.ps1**.
2. Indítsa el a PowerShell ISE-t rendszergazdaként.
3. Írja be a parancssorba<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>és nyomja le az ENTER billentyűt.
4. A PowerShell ISE-ben nyissa meg a **.ps1** fájlt. Futtassa a szkriptet.
5. A szkript először elindul egy új ablak, amelyben a bejelentkezést az Azure-bA.
   
   * Ha Újrafuttatja a parancsfájl a munkamenet megszakítása nélkül, lehetősége van kényelmesen ki megjegyzéseket, a **Add-AzureAccount** parancsot.

![PowerShell ISE-ben, az Azure-modul telepítve, és készen állnak a szkript futtatásához.][30_powershell_ise]

### <a name="powershell-code"></a>PowerShell-kód

Ez a PowerShell-parancsfájl feltételezi, hogy már futtatta a parancsmagot Import-Module az AzureRm-modul. A referencia-dokumentációja: [PowerShell-Modulböngészőt](https://docs.microsoft.com/powershell/module/).

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

$storageAccountLocation = 'West US';
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

If ($storageAccountName)
{
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
Try
{
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
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


Jegyezze fel a néhány névvel ellátott értékek, amelyek a PowerShell-parancsfájl kinyomtat végződik. A 2. fázis, a következő Transact-SQL parancsfájl, szerkesztenie kell ezeket az értékeket.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>2. fázis: A Transact-SQL kód által használt Azure Storage-tároló

* Ez a kódminta 1 fázisában futtatta egy PowerShell-példaszkript egy Azure Storage-tároló létrehozása.
* Ezután a 2. fázis, a következő Transact-SQL parancsfájl kell a tárolót használja.

A szkript után, egy korábbi lehet futtatni, rerunnable karbantartása parancsok kezdődik.

A PowerShell-szkript néhány névvel ellátott értékek nyomtatva, véget ért. Szerkesztenie kell a Transact-SQL parancsfájlt használja ezeket az értékeket. Keresés **TODO** keresse meg a csomópontok szerkesztése a Transact-SQL parancsfájl.

1. Open SQL Server Management Studio (ssms.exe).
2. Az Azure SQL Database-adatbázishoz csatlakozhat.
3. Kattintson ide egy új lekérdezési ablak megnyitásához.
4. Illessze be a következő Transact-SQL parancsfájl a lekérdezési ablaktáblába.
5. Keresés minden **TODO** a szkriptben, és adja meg a megfelelő módosításokat.
6. Mentse, és futtassa a szkriptet.


> [!WARNING]
> A SAS-kulcs értéke az előző PowerShell-parancsfájl által előállított kezdődhet a "?" (kérdőjel). Ha a következő T-SQL parancsfájl az SAS-kulcsot használ, akkor meg kell *távolítsa el a vezető "?"* . Ellenkező esetben a munkája alapján a security érdemes lehet blokkolni.


### <a name="transact-sql-code"></a>Transact-SQL code

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


Ha a cél nem csatolhat a futtatásakor, kell állítsa le és indítsa újra az esemény-munkamenethez:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Kimenet

A Transact-SQL parancsfájl befejezése után kattintson a cellára a **event_data_XML** oszlop fejlécére. Egy **<event>** elem jelenik meg, amely egy UPDATE utasítás jeleníti meg.

Egy **<event>** elem, amely a tesztelés során jött létre:


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


Az előző Transact-SQL-szkript a következő rendszer függvényt használta a event_file olvasása:

* [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

Bővített események adatainak megtekintését a Speciális beállítások magyarázatát érhető el:

* [Speciális a bővített események cél adatainak megtekintése](https://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Az SQL Server-kiszolgálón futtassa a kódminta alakítása

Tegyük fel, hogy az előző Transact-SQL-minta futtatása a Microsoft SQL Server.

* Az egyszerűség kedvéért teljesen cserélje le az Azure Storage-tároló használata például egy egyszerű fájlt szeretne **C:\myeventdata.xel**. Az SQL Servert futtató számítógépen a helyi merevlemezre tartalmazná a fájlt.
* Nem kell a Transact-SQL utasításokkal bármilyen típusú **FŐKULCSÁNAK létrehozásához** és **hitelesítő adat létrehozása**.
* Az a **munkamenet esemény létrehozása** utasítás, a annak **cél hozzáadása** záradék esetében cserélje a hozzárendelt Http érték végzett **filename =** példáulegyteljeselérésiútjakarakterlánccal**C:\myfile.xel**.
  
  * Nem Azure Storage-fiókot kell kell vonni.

## <a name="more-information"></a>További információ

További információ a fiókok és a tárolók az Azure Storage szolgáltatásban lásd:

* [A Blob storage a .NET használatával](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Tárolók, blobok és metaadatok elnevezése és hivatkozása](https://msdn.microsoft.com/library/azure/dd135715.aspx)
* [A legfelső szintű tároló használata](https://msdn.microsoft.com/library/azure/ee395424.aspx)
* [1. lecke: Egy tárolt hozzáférési szabályzat és a egy közös hozzáférésű jogosultságkód létrehozása egy Azure container](https://msdn.microsoft.com/library/dn466430.aspx)
  * [2. lecke: Közös hozzáférésű jogosultságkódok használata az SQL Server hitelesítő adatok létrehozása](https://msdn.microsoft.com/library/dn466435.aspx)
* [A Microsoft SQL Server bővített események](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

