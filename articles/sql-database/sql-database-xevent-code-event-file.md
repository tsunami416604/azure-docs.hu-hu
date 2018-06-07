---
title: SQL-adatbázis XEvent Eseményfájlt kód |} Microsoft Docs
description: PowerShell és a Transact-SQL biztosít egy kétfázisú példakód azt mutatja be az esemény cél az az Azure SQL Database-kiterjesztett esemény. Az Azure Storage ebben a forgatókönyvben egy kötelező részét képezi.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: b905c921ae967d7f755f084bd6b9b30de34f76b4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649635"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Fájl cél eseménykód kiterjesztett események az SQL-adatbázis

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Szeretne teljes kódminta kiterjesztett esemény-készítés és a jelentés adatai hatékony módot kínál.

A Microsoft SQL Server a [esemény cél](http://msdn.microsoft.com/library/ff878115.aspx) esemény kimenetek a helyi merevlemez-meghajtóról fájlban tárolja. Azonban az ilyen fájlok nem érhetők el az Azure SQL Database. Az Azure Storage szolgáltatás helyette az esemény cél használható használjuk.

Ez a témakör egy kétfázisú példakód mutatja be:

* PowerShell, hozzon létre egy Azure Storage tárolót a felhőben.
* Transact-SQL:
  
  * Az Azure Storage-tároló hozzárendelése egy esemény célhoz.
  * Hozzon létre és az esemény-munkamenet elindítása, és így tovább.

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Regisztrálhat [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Létrehozhat egy olyan táblázatában adatbázisoknak.
  
  * Igény szerint is [hozzon létre egy **AdventureWorksLT** mintaadatbázis](sql-database-get-started.md) perc múlva.
* SQL Server Management Studio (ssms.exe) ideális esetben a havi frissítés letöltéséhez. 
  Letöltheti a legújabb ssms.exe:
  
  * Című témakör [töltse le az SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [A letöltés egy közvetlen hivatkozást.](http://go.microsoft.com/fwlink/?linkid=616025)
* Rendelkeznie kell a [Azure PowerShell-modulok](http://go.microsoft.com/?linkid=9811175) telepítve.
  
  * A modulok tartalmaznak, mint - parancsok **New-AzureStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>1. fázis: PowerShell-kódjába Azure Storage-tároló

A PowerShell a kétfázisú kódminta 1. fázis.

A parancsfájl parancsok memóriából való eltávolítása után, a korábbi valószínűleg futtassa, rerunnable kezdődik.

1. A PowerShell parancsfájl illessze be egy egyszerű szövegszerkesztőben, például a Notepad.exe, és mentse a parancsfájlt a kiterjesztésű fájlként **.ps1**.
2. Indítsa el a PowerShell ISE rendszergazdaként.
3. Írja be a parancssorba<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>és nyomja le az ENTER billentyűt.
4. A PowerShell ISE, nyissa meg a **.ps1** fájlt. Futtassa a szkriptet.
5. A parancsfájl először elindul, amelyben jelentkezik be a Azure új ablakban.
   
   * Ha a parancsfájl Újrafuttatja a munkamenet megszakítása nélkül, lehetősége van tetszés szerinti a kimenő fűzött megjegyzések a **Add-AzureAccount** parancsot.

![PowerShell ISE, az Azure-moduljának telepített, készen áll a parancsfájl futtatásához.][30_powershell_ise]

### <a name="powershell-code"></a>PowerShell-kódot

A PowerShell parancsfájl feltételezi, hogy már futtatta a parancsmagot Import-Module AzureRm modul. Az útmutató dokumentációjáért lásd: [PowerShell modul böngésző](https://docs.microsoft.com/powershell/module/).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzureRmAccount (or  Connect-AzureRmAccount), just one time.';
#Connect-AzureRmAccount;   # Same as  Connect-AzureRmAccount.

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

Select-AzureRmSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

If ($storageAccountName)
{
    Remove-AzureRmStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzureRmStorageAccount `
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
    (Get-AzureRmStorageAccountKey `
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

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzureStorageContainerStoredAccessPolicy `
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
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
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
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzureRmStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```


Jegyezze fel a PowerShell parancsfájl kiírja, amikor ez befejeződik néhány nevesített értékek. A 2. fázis, a következő Transact-SQL parancsfájl szerkesztenie kell ezeket az értékeket.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>2. fázis: Transact-SQL Azure-tárolót használó kódot

* A fenti 1 fázisa futtatta egy Azure Storage-tároló létrehozása a PowerShell-parancsfájlt.
* Ezután a 2. fázis, a következő Transact-SQL parancsfájlt kell használnia a tároló.

A parancsfájl parancsok memóriából való eltávolítása után, a korábbi valószínűleg futtassa, rerunnable kezdődik.

A PowerShell parancsfájl néhány névvel ellátott értékek nyomtatva ért véget. Módosítania kell a Transact-SQL parancsfájlt használja ezeket az értékeket. Található **TODO** a Transact-SQL parancsfájl keresse meg a csomópontok szerkesztése.

1. Nyissa meg az SQL Server Management Studio (ssms.exe).
2. Csatlakozás az Azure SQL Database adatbázishoz.
3. Kattintson ide egy új lekérdezési ablak megnyitásához.
4. A következő Transact-SQL parancsfájl illessze be a lekérdezési ablaktáblában.
5. Található minden **TODO** a parancsfájl és elvégezni a megfelelő módosításokat.
6. Mentse, és futtassa a parancsfájlt.


> [!WARNING]
> A SAS-kulcs értékét a fenti PowerShell-parancsfájl által generált kezdődhet a "?" (kérdőjel). Ha a következő T-SQL-parancsfájlt az SAS-kulcsot használ, akkor meg kell *távolítsa el a bevezető "?"* . Ellenkező esetben a próbálkozások biztonsági blokkolhatja.


### <a name="transact-sql-code"></a>Transact-SQL-kódot

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

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


Ha a cél nem csatolható futtatásakor, akkor állítsa le és indítsa újra az esemény-munkamenet:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Kimenet

A Transact-SQL parancsfájl befejezése után kattintson a cella a **event_data_XML** oszlop fejlécére. Egy **<event>** elem akkor jelenik meg, amely egy UPDATE utasítás mutatja.

Itt az egyik **<event>** elem, amely a tesztelés során jött létre:


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


Az előző Transact-SQL-parancsfájlt a következő rendszerfüggvény a event_file olvasásához használt:

* [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)

Speciális beállítások a megtekintésre kiterjesztett események adatainak magyarázata érhető el:

* [Speciális cél kiterjesztett események adatainak megtekintése](http://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Az SQL Server futtatásához kódminta alakítása

Tegyük fel, hogy szeretné futtatni a Transact-SQL előző példa a Microsoft SQL Server.

* Az egyszerűség kedvéért szeretné teljesen cserélje le az Azure Storage-tároló használja, mint egy egyszerű fájl **C:\myeventdata.xel**. A fájl tartalmazná a számítógép, amelyen az SQL Server a helyi merevlemez-meghajtóról.
* Nem kell semmilyen Transact-SQL-utasításainak **FŐKULCS létrehozása** és **hitelesítő adat létrehozása**.
* Az a **munkamenet esemény létrehozása** utasítás, a a **tároló hozzáadása** záradék, cserélje a hozzárendelt Http érték végzett **filename =** például a teljes elérési útja karakterláncot **C:\myfile.xel**.
  
  * Nincs Azure-tárfiók be kell vonni.

## <a name="more-information"></a>További információ

Fiókok és az Azure Storage szolgáltatást a tárolók kapcsolatos további információkért lásd:

* [A .NET-Blob-tároló használata](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Tárolók, blobok és metaadatok elnevezése és hivatkozása](http://msdn.microsoft.com/library/azure/dd135715.aspx)
* [A gyökérszintű tároló használata](http://msdn.microsoft.com/library/azure/ee395424.aspx)
* [1. lecke: Egy tárolt hozzáférési házirend és a közös hozzáférésű jogosultságkód létrehozása egy Azure-tárolót a](http://msdn.microsoft.com/library/dn466430.aspx)
  * [2. lecke: SQL Server hitelesítő adatok használatával a közös hozzáférésű jogosultságkód létrehozása](http://msdn.microsoft.com/library/dn466435.aspx)
* [A Microsoft SQL Server kiterjesztett események](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

