---
title: AAD engedélyezése az Azure SSIS-integrációs futásórához
description: Ez a cikk bemutatja, hogyan engedélyezheti az Azure Active Directory-hitelesítést az Azure Data Factory felügyelt identitásával az Azure-SSIS-integrációs futásidő létrehozásához.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 2359b378b1f54cf6e03218f819b3a7c5740ba596
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416407"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure Active Directory-hitelesítés engedélyezése az Azure-SSIS integrációs modulhoz

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk bemutatja, hogyan engedélyezheti az Azure Active Directory (Azure AD) hitelesítését az Azure Data Factory (ADF) felügyelt identitásával, és hogyan használhatja azt a hagyományos hitelesítési módszerek (például az SQL-hitelesítés) helyett:

- Hozzon létre egy Azure-SSIS-integrációs runtime (IR), amely viszont kiépítése SSIS katalógus adatbázis (SSISDB) az Azure SQL Database server/managed instance az Ön nevében.

- Az Azure-SSIS IR-n ssis-csomagok futtatásakor különböző Azure-erőforrásokhoz csatlakozhat.

Az ADF felügyelt identitásáról a Data [Factory felügyelt identitása](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)című témakörben talál további információt.

> [!NOTE]
>-  Ebben a forgatókönyvben az Azure AD-hitelesítés az ADF felügyelt identitásával csak az SSIS IR létrehozása és későbbi indítása során használatos, amelyek viszont kiépítik és csatlakoznak az SSISDB-hez. Az SSIS-csomagok végrehajtása esetén az SSIS IR továbbra is az SSISDB-hez csatlakozik az SQL-hitelesítés használatával, az SSISDB-kiépítés során létrehozott teljes körűen felügyelt fiókokkal.
>-  Ha már létrehozta az SSIS IR-t SQL-hitelesítéssel, jelenleg nem konfigurálhatja újra az Azure AD-hitelesítés t a PowerShellen keresztül, de ezt az Azure Portalon/ADF-alkalmazáson keresztül teheti meg. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Az Azure AD engedélyezése az Azure SQL-adatbázisban

Az Azure SQL Database Server támogatja az azure-beli AD-felhasználóval létrehozott adatbázis létrehozását. Először létre kell hoznia egy Azure AD-csoportot az ADF tagként felügyelt identitásával. Ezután be kell állítania egy Azure AD-felhasználót az Azure SQL Database-kiszolgáló Active Directory-rendszergazdájaként, majd csatlakoznia kell hozzá az SQL Server Management Studio (SSMS) használatával az adott felhasználó használatával. Végül létre kell hoznia egy, az Azure AD csoportot képviselő, egy tartalmazott felhasználót, így az ADF felügyelt identitását az Azure-SSIS IR az Ön nevében ssisdb létrehozásához használhatja.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Hozzon létre egy Azure AD-csoportot az ADF tagként kezelt identitásával

Használhatja a meglévő Azure AD-csoportot, vagy hozzon létre egy újat az Azure AD PowerShell használatával.

1.  Telepítse az [Azure AD PowerShell-modult.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)

2.  Jelentkezzen be `Connect-AzureAD`a használatával, futtassa a következő parancsmast egy csoport létrehozásához, és mentse egy változóban:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Az eredmény a következő példához hasonlóan jelenik meg, amely a változó értékét is megjeleníti:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Adja hozzá az ADF felügyelt identitását a csoporthoz. A [Data Factory felügyelt identitásának](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) követésével lekéri a fő felügyelt identitásobjektum-azonosítót (pl. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, de erre a célra ne használja a felügyelt identitásalkalmazás-azonosítót).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Ezt követően a csoporttagságot is ellenőrizheti.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Azure AD-hitelesítés konfigurálása az Azure SQL Database-kiszolgálóhoz

Az [Azure AD-hitelesítést az ALÁBBI lépésekkel konfigurálhatja és kezelheti SQL-nel:](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

1.  Az Azure Portalon válassza a bal oldali navigáció **minden szolgáltatás** -> **SQL-kiszolgálója** lehetőséget.

2.  Válassza ki az Azure Sql Database-kiszolgálót, amelyet az Azure AD-hitelesítéssel szeretne konfigurálni.

3.  A panel **Beállítások** szakaszában válassza az **Active Directory rendszergazdája**lehetőséget.

4.  A parancssávon válassza a **Rendszergazda beállítása lehetőséget.**

5.  Válasszon ki egy Azure AD felhasználói fiókot, amelyet a kiszolgáló rendszergazdájává szeretne tenni, majd válassza a **Kijelölés lehetőséget.**

6.  A parancssávon válassza a **Mentés gombot.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Az Azure AD-csoportot képviselő, zárt felhasználó létrehozása az Azure SQL Database-kiszolgálón

Ehhez a következő lépéshez a [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) szükséges.

1. Indítsa el az SSMS-t.

2. A **Csatlakozás a kiszolgálóhoz** párbeszédpanelen adja meg az Azure SQL Database-kiszolgáló nevét a **Kiszolgáló neve** mezőben.

3. A **Hitelesítés** mezőben válassza az **Active Directory – Univerzális mfa-támogatás** lehetőséget (a másik két Active Directory-hitelesítési típust is [használhatja, lásd: Az Azure AD-hitelesítés konfigurálása és kezelése SQL-sel).](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

4. A **Felhasználónév** mezőbe írja be a kiszolgáló rendszergazdaként beállított Azure AD-fiókjának nevét, például . testuser@xxxonline.com

5. Válassza a **Csatlakozás** lehetőséget, és fejezze be a bejelentkezési folyamatot.

6. Az **Objektumkezelőben** **bontsa** -> ki az Adatbázisok**rendszeradatbázisai mappát.**

7. Kattintson a **master** jobb gombbal a főadatbázisra, és válassza az **Új lekérdezés parancsot.**

8. A lekérdezésablakban írja be a következő T-SQL parancsot, és kattintson a **Végrehajtás gombra** az eszköztáron.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   A parancsnak sikeresen el kell végeznie, és a csoportot egy zárt felhasználót hoz létre.

9. Törölje a lekérdezési ablakot, írja be a következő T-SQL parancsot, és válassza a **Végrehajtás gombot** az eszköztáron.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   A parancsnak sikeresen el kell végeznie, lehetővé teszi a tartalmazott felhasználó számára az adatbázis (SSISDB) létrehozását.

10. Ha az SSISDB-t SQL-hitelesítéssel hozták létre, és az Azure-SSIS ir-hez azure AD-hitelesítést szeretne használni, kattintson a jobb gombbal az **SSISDB** adatbázisra, és válassza az **Új lekérdezés**parancsot.

11. A lekérdezésablakban írja be a következő T-SQL parancsot, és kattintson a **Végrehajtás gombra** az eszköztáron.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    A parancsnak sikeresen el kell végeznie, és a csoportot egy zárt felhasználót hoz létre.

12. Törölje a lekérdezési ablakot, írja be a következő T-SQL parancsot, és válassza a **Végrehajtás gombot** az eszköztáron.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    A parancsnak sikeresen el kell végeznie, lehetővé tetve a foglalt felhasználónak az SSISDB elérését.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Az Azure AD engedélyezése az Azure SQL Database felügyelt példányán

Az Azure SQL Database felügyelt példány a közvetlenül az ADF felügyelt identitásával rendelkező adatbázis létrehozását támogatja. Nem kell csatlakoznia az ADF felügyelt identitásához egy Azure AD-csoporthoz, és nem kell létrehoznia egy a felügyelt példányban az adott csoportot képviselő, egy bent lévő felhasználót.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Azure AD-hitelesítés konfigurálása az Azure SQL Database felügyelt példányához

Kövesse az [Azure Active Directory-rendszergazda kiépítése a felügyelt példányhoz](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance)című lépések lépéseit.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Az ADF felügyelt identitásának hozzáadása felhasználóként az Azure SQL Database felügyelt példányában

Ehhez a következő lépéshez a [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) szükséges.

1.  Indítsa el az SSMS-t.

2.  Csatlakozzon a felügyelt példányhoz egy **rendszergazdai**rendszergazdai sql Server-fiókkal. Ez egy ideiglenes korlátozás, amely akkor lesz eltávolítva, ha az Azure SQL Database Felügyelt példány Azure AD-kiszolgálóinak egyszerű (bejelentkezési) rendszerga- vá válik. A következő hibaüzenet jelenik meg, ha egy Azure AD-rendszergazdai fiók kal konként próbál létrehozni a bejelentkezést: Msg 15247, Level 16, State 1, Line 1 Felhasználó nem rendelkezik engedéllyel a művelet végrehajtásához.

3.  Az **Objektumkezelőben** **bontsa** -> ki az Adatbázisok**rendszeradatbázisai mappát.**

4.  Kattintson a **master** jobb gombbal a főadatbázisra, és válassza az **Új lekérdezés parancsot.**

5.  A lekérdezési ablakban hajtsa végre a következő T-SQL parancsfájlt az ADF felügyelt identitásának felhasználóként való hozzáadásához

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    A parancsnak sikeresen el kell végeznie, így az ADF felügyelt identitása lehetővé teszi az adatbázis (SSISDB) létrehozását.

6.  Ha az SSISDB-t SQL-hitelesítéssel hozták létre, és az Azure-SSIS ir-hez azure AD-hitelesítést szeretne használni, kattintson a jobb gombbal az **SSISDB** adatbázisra, és válassza az **Új lekérdezés**parancsot.

7.  A lekérdezésablakban írja be a következő T-SQL parancsot, és kattintson a **Végrehajtás gombra** az eszköztáron.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    A parancsnak sikeresen el kell végeznie, így az ADF felügyelt identitása lehetővé teszi az SSISDB elérését.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure-SSIS IR kiépítése az Azure Portalon/ADF-alkalmazásban

Amikor az Azure-SSIS IR-t az Azure Portalon/ADF-alkalmazásban építi ki, az **SQL Settings (Sql Settings)** lapon válassza **az AAD-hitelesítés használata az ADF-beállítás felügyelt identitásával** lehetőséget. Az alábbi képernyőképen az SSISDB-t üzemeltető Azure SQL Database-kiszolgálóval rendelkező infravörös kapcsolat beállításai láthatók. Az SSISDB-t üzemeltető felügyelt példánysal rendelkező infravörös kapcsolatra a **katalógus-adatbázis szolgáltatási szint** és az **Azure-szolgáltatások hozzáférési beállításainak engedélyezése** nem alkalmazható, míg más beállítások megegyeznek.

Az Azure-SSIS IR létrehozásáról további információt az [Azure-SSIS-integrációs futásidő létrehozása az Azure Data Factoryban című témakörben talál.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

![Az Azure-SSIS-integrációs futásidejű beállítások](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Azure-SSIS IR kiépítése a PowerShell használatával

Az Azure-SSIS-ir-t a PowerShell használatával való kiépítéséhez tegye a következőkre:

1.  Telepítse [az Azure PowerShell-modult.](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 

2.  A parancsfájlban ne `CatalogAdminCredential` állítson be paramétert. Például:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>SSIS-csomagok futtatása felügyelt identitáshitelesítéssel

Amikor SSIS-csomagokat futtat az Azure-SSIS IR-n, felügyelt identitás-hitelesítéssel csatlakozhat különböző Azure-erőforrásokhoz. Jelenleg már támogatott felügyelt identitás hitelesítésa a következő kapcsolat kezelők.

- [OLE DB csatlakozáskezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Csatlakozáskezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage-kapcsolatkezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
