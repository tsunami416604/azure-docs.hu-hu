---
title: 'Oktatóanyag: a Always Encrypted első lépései a biztonságos enklávékkal Azure SQL Database'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy alapszintű környezetet a biztonságos enklávékkal való Always Encrypted számára a Azure SQL Databaseban, és hogyan titkosíthatja az adatok helyben történő titkosítását, valamint a titkosított oszlopokkal kapcsolatos részletes bizalmas lekérdezéseket SQL Server Management Studio (SSMS) használatával.
keywords: adatok titkosítása, SQL-titkosítás, adatbázis-titkosítás, bizalmas adatok, Always Encrypted, biztonságos enklávék, SGX ENKLÁVÉHOZ, igazolás
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 94923b13181290a290f13339da5b05f6fdddff38
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253739"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Oktatóanyag: a Always Encrypted első lépései a biztonságos enklávékkal Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted a biztonságos enklávékkal a Azure SQL Database jelenleg **nyilvános előzetes** verzióban érhető el.

Ez az oktatóanyag bemutatja, hogyan kezdheti meg a [Always Encryptedt a biztonságos enklávékkal](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) Azure SQL Databaseban. A következőket fogja látni:

> [!div class="checklist"]
> - Környezet létrehozása a biztonságos enklávékkal való Always Encrypted teszteléséhez és kiértékeléséhez.
> - Az adatok helyben történő titkosítása és a titkosított oszlopokkal kapcsolatos bizalmas lekérdezések kiadása SQL Server Management Studio (SSMS) használatával.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz Azure PowerShell és [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)szükséges.

### <a name="powershell-requirements"></a>PowerShell-követelmények

A Azure PowerShell telepítésével és futtatásával kapcsolatos információkért tekintse meg a [Azure PowerShell áttekintése](https://docs.microsoft.com/powershell/azure) című témakört. 

Az igazolási műveletek támogatásához szükséges az modulok minimális verziója:

- Az 4.5.0
- Az. accounts 1.9.2
- Az. igazolási 0.1.8

Futtassa az alábbi parancsot az összes modul telepített verziójának ellenőrzéséhez:

```powershell
Get-InstalledModule
```

Ha a verziók nem felelnek meg a minimális követelménynek, futtassa a `Update-Module` parancsot.

A PowerShell-galéria elavult Transport Layer Security (TLS) 1,0-es és 1,1-es verzióval rendelkezik. A TLS 1,2 vagy újabb verzió használata javasolt. Ha 1,2-nál kisebb TLS-verziót használ, a következő hibaüzenetek jelenhetnek meg:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

A PowerShell-galéria folytatásához futtassa a következő parancsot a Install-Module parancsok előtt.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>SSMS-követelmények

A SSMS letöltésével kapcsolatos információkért tekintse meg a [letöltés SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) című témakört.

A SSMS kötelező minimális verziója 18,8.


## <a name="step-1-create-a-server-and-a-dc-series-database"></a>1. lépés: kiszolgáló és egy DC sorozatú adatbázis létrehozása

 Ebben a lépésben egy új Azure SQL Database logikai kiszolgálót és egy új adatbázist fog létrehozni a DC sorozatú hardverkonfiguráció használatával. A Azure SQL Database Secure enklávés Always Encrypted az Intel SGX ENKLÁVÉHOZ enklávés szolgáltatást használja, amelyet a DC sorozatú hardverkonfiguráció támogat. További információ: [DC-Series](service-tiers-vcore.md#dc-series).

1. Nyisson meg egy PowerShell-konzolt, és jelentkezzen be az Azure-ba. Ha szükséges, [váltson az oktatóanyaghoz használt előfizetésre](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps) .

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = <your subscription ID>
  Set-AzContext -Subscription $serverSubscriptionId
  ```

2. Hozzon létre egy erőforráscsoportot, amely tartalmazza az adatbázis-kiszolgálót. 

  ```powershell
  $serverResourceGroupName = "<server resource group name>"
  $serverLocation = "<Azure region that supports DC-series in SQL Database>"
  New-AzResourceGroup -Name $serverResourceGroupName -Location $serverLocation 
  ```

  > [!IMPORTANT]
  > Létre kell hoznia egy erőforráscsoportot egy olyan régióban, amely támogatja a DC sorozatú hardver konfigurációját. A jelenleg támogatott régiók listáját lásd: a [DC-sorozat rendelkezésre állása](service-tiers-vcore.md#dc-series-1).

3. Hozzon létre egy adatbázis-kiszolgálót. Ha a rendszer kéri, adja meg a kiszolgáló rendszergazdájának nevét és jelszavát.

  ```powershell
  $serverName = "<server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -Location $serverLocation
  ```

4. A megadott IP-címtartomány elérését lehetővé tevő kiszolgálói tűzfalszabály létrehozása
  
  ```powershell
  # The ip address range that you want to allow to access your server
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

5. Rendeljen hozzá egy felügyelt rendszeridentitást a kiszolgálóhoz. Később szüksége lesz rá, hogy hozzáférést biztosítson a kiszolgálónak Microsoft Azure igazoláshoz.

  ```powershell
  Set-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -AssignIdentity 
  ```

6. A kiszolgálóhoz hozzárendelt identitás AZONOSÍTÓjának beolvasása. Mentse az eredményül kapott objektumazonosítót. Az azonosítót egy későbbi szakaszban kell megadnia.

  > [!NOTE]
  > Eltarthat néhány másodpercig, amíg az újonnan hozzárendelt felügyelt rendszer-identitást Azure Active Directory propagálni. Ha az alábbi szkript üres eredményt ad vissza, próbálkozzon újra.

  ```PowerShell
  $server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
  $serverObjectId = $server.Identity.PrincipalId
  $serverObjectId
  ```

7. Hozzon létre egy DC sorozatú adatbázist.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $serverResourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $edition -Vcore $vCore -ComputeGeneration $generation
  ```

## <a name="step-2-configure-an-attestation-provider"></a>2. lépés: igazolási szolgáltató konfigurálása

Ebben a lépésben egy igazolási szolgáltatót fog létrehozni és konfigurálni Microsoft Azure igazolásban. Erre azért van szükség, hogy tanúsítsa a biztonságos enklávét az adatbázis-kiszolgálón.

1. Másolja az alábbi igazolási házirendet, és mentse a szabályzatot szövegfájlba (txt). További információ az alábbi szabályzatról: [igazolási szolgáltató létrehozása és konfigurálása](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Importálja a és a szükséges verzióit `Az.Accounts` `Az.Attestation` .  

  ```powershell
  Import-Module "Az.Accounts" -MinimumVersion "1.9.2"
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```

3. Hozzon létre egy erőforráscsoportot az igazolási szolgáltatóhoz.

  ```powershell
  $attestationLocation = $serverLocation
  $attestationResourceGroupName = "<attestation provider resource group name>"
  New-AzResourceGroup -Name $attestationResourceGroupName -Location $location  
  ```

4. Hozzon létre egy igazolási szolgáltatót. 

  ```powershell
  $attestationProviderName = "<attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Location $attestationLocation
  ```

5. Konfigurálja az igazolási szabályzatot.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Tee $teeType -Policy $policy -PolicyFormat  $policyFormat
  ```

6. Adja meg az Azure SQL logikai kiszolgáló hozzáférését az igazolási szolgáltatóhoz. Ebben a lépésben a korábban a kiszolgálóhoz hozzárendelt felügyelt szolgáltatás identitásának objektumazonosítót használjuk.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName  
  ```

7. A tanúsítvány URL-címének beolvasása.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host "Your attestation URL is: " $attestationUrl 
  ```

8.  Mentse az eredményül kapott igazolási URL-címet, amely a SGX ENKLÁVÉHOZ enklávéhoz konfigurált igazolási házirendre mutat. Erre később még szüksége lesz. A tanúsítvány URL-címének így kell kinéznie: `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>3. lépés: az adatbázis feltöltése

Ebben a lépésben létrehoz egy táblát, és feltölti azt néhány olyan adattal, amelyet később titkosítani és lekérdezni fog.

1. Nyissa meg a SSMS-t, és kapcsolódjon a **ContosoHR** -adatbázishoz az Ön által létrehozott Azure SQL logikai kiszolgálón **anélkül** , hogy az adatbázis-kapcsolaton engedélyezve Always encrypted.
    1. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a kiszolgáló nevét (például *myserver123.database.Windows.net*), és adja meg a korábban megadott felhasználónevet és jelszót.
    2. Kattintson a **beállítások >>** elemre, és válassza a **kapcsolatok tulajdonságai** lapot. Ügyeljen arra, hogy a **ContosoHR** -adatbázist (nem az alapértelmezett főadatbázist) válassza ki. 
    3. Válassza a **Always encrypted** lapot.
    4. Győződjön meg arról, hogy az **Enable Always encrypted (oszlop titkosítása)** jelölőnégyzet **nincs** bejelölve.

        ![Always Encrypted nélküli kapcsolat](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Kattintson a **Csatlakozás** gombra.

2. Hozzon létre egy új, **alkalmazottak** nevű táblát.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Vegyen fel néhány alkalmazotti rekordot az **Employees** táblába.

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>4. lépés: az enklávé használatára képes kulcsok kiépítése

Ebben a lépésben létrehoz egy oszlop főkulcsát és egy olyan oszlop-titkosítási kulcsot, amely engedélyezi az enklávé számításait.

1. Az előző lépésben használt SSMS-példány használatával **Object Explorer** bontsa ki az adatbázist, és navigáljon a **biztonsági**  >  **Always encrypted kulcsaihoz**.
1. Új enklávé-kompatibilis oszlop főkulcsának kiépítése:
    1. Kattintson a jobb gombbal **Always encrypted kulcsok** elemre, és válassza az **új oszlop főkulcsa...** lehetőséget.
    2. Válassza ki az oszlop főkulcsának nevét: **CMK1**.
    3. Győződjön meg arról, hogy a **Windows tanúsítványtároló (aktuális felhasználó vagy helyi gép)** vagy **Azure Key Vault** lehetőséget választotta.
    4. Válassza az **enklávé-számítások engedélyezése** lehetőséget.
    5. Ha a Azure Key Vault lehetőséget választotta, jelentkezzen be az Azure-ba, és válassza ki a kulcstartót. A Always Encrypted kulcstartójának létrehozásával kapcsolatos további információkért lásd: [a kulcstartók kezelése Azure Portalról](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal).
    6. Ha már létezik, válassza ki a tanúsítvány vagy az Azure-kulcs értéke kulcsot, vagy kattintson a **tanúsítvány létrehozása** gombra egy új létrehozásához.
    7. Válassza az **OK** lehetőséget.

        ![Enklávé-számítások engedélyezése](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Új enklávé-kompatibilis oszlop titkosítási kulcsának létrehozása:

    1. Kattintson a jobb gombbal **Always encrypted kulcsok** elemre, és válassza az **új oszlop titkosítási kulcs** elemet.
    2. Adja meg az új oszlop titkosítási kulcsának nevét: **CEK1**.
    3. Az **oszlop főkulcsának** legördülő listájában válassza ki az előző lépésekben létrehozott oszlop főkulcsát.
    4. Válassza az **OK** lehetőséget.

## <a name="step-5-encrypt-some-columns-in-place"></a>5. lépés: egyes oszlopok titkosítása a helyükön

Ebben a lépésben a **Taj** -ben és a **fizetési** oszlopokban tárolt adategységeket fogja titkosítani a kiszolgálóoldali enklávéban, majd tesztelje a Select lekérdezést az adaton.

1. Nyisson meg egy új SSMS-példányt, és kapcsolódjon **az adatbázishoz Always encrypted engedélyezve** van az adatbázis-kapcsolathoz.
    1. Indítsa el a SSMS új példányát.
    2. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a kiszolgáló nevét, válasszon ki egy hitelesítési módszert, és adja meg a hitelesítő adatait.
    3. Kattintson a **beállítások >>** elemre, és válassza a **kapcsolatok tulajdonságai** lapot. Ügyeljen arra, hogy a **ContosoHR** -adatbázist (nem az alapértelmezett főadatbázist) válassza ki. 
    4. Válassza a **Always encrypted** lapot.
    5. Győződjön meg arról, hogy a **Always encrypted engedélyezése (oszlop titkosítása)** jelölőnégyzet be van jelölve.
    6. Adja meg az enklávé igazolási URL-címét, amelyet a [2. lépés: igazolási szolgáltató konfigurálása című szakasz](#step-2-configure-an-attestation-provider)lépéseit követve kapott. Lásd az alábbi képernyőképet.

        ![Kapcsolat igazolással](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Válassza a **Kapcsolódás** lehetőséget.
    8. Ha a rendszer a paraméterezés engedélyezését kéri Always Encrypted lekérdezésekhez, válassza az **Engedélyezés** lehetőséget.



1. Ha ugyanazt az SSMS-példányt (Always Encrypted engedélyezve) használja, nyisson meg egy új lekérdezési ablakot, és az alábbi utasítások futtatásával Titkosítsa a **SSN** és a **fizetési** oszlopot.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Figyelje meg, hogy az ALTER DATABASE HATÓKÖRű konfiguráció CLEAR PROCEDURE_CACHE utasítással törli a fenti parancsfájlban található adatbázishoz tartozó lekérdezési terv gyorsítótárát. Miután módosította a táblázatot, törölnie kell a csomagokat minden köteghez és tárolt eljáráshoz, amely hozzáfér a táblához a paraméterek titkosítási információinak frissítéséhez. 

1. Annak ellenőrzéséhez, hogy az **SSN** és a **fizetési** oszlopok már titkosítva vannak-e, nyisson meg egy új LEKÉRDEZÉSi ablakot az SSMS-példányon anélkül, hogy az adatbázis-kapcsolatban Always encrypted engedélyezve, és végre **kellene** hajtania az alábbi A lekérdezési ablaknak titkosított értékeket kell visszaadnia az **SSN** és a **munkabér** oszlopban. Ha ugyanazt a lekérdezést a SSMS-példány használatával hajtja végre, Always Encrypted engedélyezve van, akkor a visszafejtett adatértéknek kell megjelennie.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>6. lépés: gazdag lekérdezések futtatása titkosított oszlopokon

A titkosított oszlopokon is futtathat részletes lekérdezéseket. Néhány lekérdezés-feldolgozás a kiszolgálóoldali enklávéban lesz elvégezve. 

1. Győződjön meg arról, hogy a SSMS **-példányban engedélyezve van a** Always encrypted, hogy a Always encrypted paraméterezés is engedélyezve van-e.
    1. Válassza az **eszközök** lehetőséget a SSMS főmenüjében.
    2. Válassza a **beállítások... lehetőséget**.
    3. Navigáljon a **lekérdezés-végrehajtás**  >  **SQL Server**  >  **speciális** elemre.
    4. Ellenőrizze, hogy be van-e jelölve **a Always encrypted paraméterezés engedélyezése** jelölőnégyzet.
    5. Válassza az **OK** lehetőséget.
2. Nyisson meg egy új lekérdezési ablakot, illessze be az alábbi lekérdezést, majd hajtsa végre a parancsot. A lekérdezésnek szöveges értékeket és sorokat kell visszaadnia, amelyek megfelelnek a megadott keresési feltételeknek.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Próbálkozzon újra ugyanazzal a lekérdezéssel abban a SSMS-példányban, amelyhez nincs Always Encrypted engedélyezve. Hiba lép fel.
 
## <a name="next-steps"></a>További lépések

Az oktatóanyag elvégzése után az alábbi oktatóanyagok közül választhat:
- [Oktatóanyag: .NET-alkalmazások fejlesztése a Always Encrypted használatával biztonságos enklávékkal](https://docs.microsoft.com/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Oktatóanyag: .NET-keretrendszerbeli alkalmazás fejlesztése a Always Encrypted használatával biztonságos enklávékkal](https://docs.microsoft.com/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Oktatóanyag: az enklávé-kompatibilis oszlopokban lévő indexek létrehozása és használata véletlenszerű titkosítással](https://docs.microsoft.com/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Lásd még:

- [Always Encrypted konfigurálása és használata biztonságos enklávékkal](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)