---
title: Az Azure-SSIS integrációs modul az Azure Active Directory-hitelesítés engedélyezése |} A Microsoft Docs
description: Ez a cikk ismerteti azokat a felügyelt identitáshoz tartozó Azure Data Factory létrehozása az Azure-SSIS integrációs modul az Azure Active Directory-hitelesítés engedélyezéséhez.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 12/25/2018
ms.author: douglasl
ms.openlocfilehash: be14eb59cb89676b0d69b94246f35ad6dfc7eed9
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792647"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Az Azure-SSIS integrációs modul az Azure Active Directory-hitelesítés engedélyezése

Ez a cikk bemutatja, hogyan azokat a felügyelt identitáshoz az Azure Data Factory (ADF) az Azure Active Directory (Azure AD-) hitelesítés engedélyezése, és ezzel az SQL-hitelesítés helyett hozzon létre egy Azure-SSIS integrációs modul (IR), amely ezután hozza létre az SSIS katalógusadatbázist (SSISDB) az Azure SQL Database server/Managed Instance az Ön nevében.

A felügyelt identitást az ADF használatával kapcsolatos további információkért lásd: [Azure Data Factory-szolgáltatásidentitás](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Ha már létrehozott egy Azure-SSIS integrációs modul SQL-hitelesítéssel, újrakonfigurálhatja az integrációs modul az Azure AD-hitelesítés használatához a PowerShell-lel jelenleg nem, de ehhez az Azure portal/ADF-alkalmazásban. 

## <a name="enable-azure-ad-on-azure-sql-database"></a>Az Azure SQL Database az Azure AD engedélyezése

Azure SQL Database-kiszolgáló támogatja az adatbázis létrehozása az Azure AD-felhasználót. Először egy Azure AD-csoport létrehozása az ADF nevű felügyelt identitással. Ezután meg kell egy Azure AD-felhasználó állítja be az Active Directory-rendszergazda az Azure SQL Database-kiszolgáló, és hogyan csatlakozhat ahhoz az SQL Server Management Studio (SSMS) használatával, hogy a felhasználó. Végül szeretne létrehozni egy felhasználó az Azure AD-csoport jelölő, így a felügyelt identitást az ADF használhatja az Azure-SSIS integrációs Modult hozhat létre az SSISDB az Ön nevében.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Az Azure AD-csoport létrehozása a felügyelt identitás számára az ADF tagként

Egy meglévő Azure AD-csoportot, vagy hozzon létre egy új Azure AD PowerShell használatával.

1.  Telepítse a [az Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul.

2.  Jelentkezzen be a `Connect-AzureAD`, hozzon létre egy csoportot a következő parancsmag futtatásával, és mentse azt egy változóban:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Az eredmény a következő példának, amely megjeleníti a változó értékét is hasonlóan néz ki:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  A felügyelt identitás számára az ADF hozzáadása a csoporthoz. Kövesse a cikk [Azure Data Factory-szolgáltatásidentitás](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) beolvasni az egyszerű SZOLGÁLTATÁSIDENTITÁS azonosítója (pl. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, de erre a célra ne használja a SZOLGÁLTATÁSIDENTITÁS Alkalmazásazonosítója).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    A csoporttagság később is ellenőrizheti.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Az Azure SQL Database-kiszolgálóhoz az Azure AD-hitelesítés konfigurálása

Is [konfigurálása és kezelése az Azure AD-hitelesítés az SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) az alábbi lépéseket követve:

1.  Az Azure Portalon, válassza ki a **minden szolgáltatás** -> **SQL Server-kiszolgálók** a bal oldali navigációs sávon.

2.  Válassza ki az Azure SQL Database-kiszolgálóhoz kell konfigurálni az Azure AD-hitelesítés.

3.  Az a **beállítások** a panel, válassza ki a szakasz **Active Directory-rendszergazda**.

4.  A parancssávon válassza ki a **rendszergazda beállítása**.

5.  Válassza ki az Azure AD felhasználói fiók lehet végezni a kiszolgáló rendszergazdája, és válassza ki a **válassza ki.**

6.  A parancssávon válassza ki a **mentéséhez.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Egy felhasználó létrehozása az Azure SQL Database-kiszolgáló jelölő, az Azure AD-csoport

A következő lépéshez szükséges [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  SSMS elindításához.

2.  Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a az Azure SQL Database-kiszolgáló nevét a **kiszolgálónév** mező.

3.  Az a **hitelesítési** mezőben válassza **Active Directory - Universal MFA-támogatással rendelkező** (is használhatja a másik két Active Directory hitelesítési típus, lásd: [ Konfigurálhatja és kezelheti az Azure AD-hitelesítés az SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4.  Az a **felhasználónév** mezőben adja meg az Ön által beállított kiszolgálói rendszergazdaként, például az Azure AD-fiók neve testuser@xxxonline.com.

5.  Válassza ki **Connect** és a bejelentkezési folyamat befejezéséhez.

6.  Az a **Object Explorer**, bontsa ki a **adatbázisok** -> **rendszeradatbázisok** mappát.

7.  Kattintson a jobb gombbal a **fő** adatbázisra, majd válassza **új lekérdezés**.

8.  A lekérdezési ablakban adja meg a következő T-SQL-parancsot, és válassza ki **Execute** az eszköztáron.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    A parancs sikeresen befejeződik, egy felhasználó, amelyek a csoport létrehozása.

9.  A lekérdezési ablakban törölje, adja meg a következő T-SQL-parancsot, majd válassza **Execute** az eszköztáron.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    A parancs sikeresen befejeződik, a felhasználó jegykiadó képes létrehozni egy adatbázist.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példány az Azure AD engedélyezése

Az Azure SQL Database felügyelt példánya támogatja a hoz létre egy adatbázist a felügyelt identitáshoz az ADF közvetlenül. Meg kell nem a felügyelt identitás csatlakozzon az Azure Active Directory-csoportnak az ADF, és egy azt a csoportot a felügyelt példány jelölő felhasználó létrehozásához.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példány az Azure AD-hitelesítés konfigurálása

1.   Az Azure Portalon, válassza ki a **minden szolgáltatás** -> **SQL Server-kiszolgálók** a bal oldali navigációs sávon.

1.   Válassza ki a felügyelt példány az Azure AD-hitelesítést kell konfigurálni.

1.   Az a **beállítások** a panel, válassza ki a szakasz **Active Directory-rendszergazda**.

1.   A parancssávon válassza ki a **rendszergazda beállítása**.

1.   Válassza ki az Azure AD felhasználói fiók lehet végezni a kiszolgáló rendszergazdája, és válassza ki a **kiválasztása**.

1.   A parancssávon válassza ki a **mentése**.

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Adja hozzá a felügyelt identitás számára az ADF felhasználóként az Azure SQL Database felügyelt példánya

A következő lépéshez szükséges [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  SSMS elindításához.

2.  Csatlakozhat a felügyelt példány az SQL/Active Directory-rendszergazdai fiók használatával.

3.  Az a **Object Explorer**, bontsa ki a **adatbázisok** -> **rendszeradatbázisok** mappát.

4.  Kattintson a jobb gombbal a **fő** adatbázisra, majd válassza **új lekérdezés**.

5.  Szerezze be a felügyelt identitás az ADF. Kövesse a cikk [Azure Data Factory-szolgáltatásidentitás](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) való lekérése az egyszerű szolgáltatás IDENTITÁS Alkalmazásazonosítója (de ne használja erre a célra SZOLGÁLTATÁSIDENTITÁS azonosítója).

6.  A lekérdezési ablakban hajtsa végre a következő T-SQL parancsfájl konvertálása a felügyelt identitás a bináris típusú, az ADF:

    ```sql
    DECLARE @applicationId uniqueidentifier = {your SERVICE IDENTITY APPLICATION ID}
    select CAST(@applicationId AS varbinary)
    ```
    
    A parancs sikeresen befejeződik, a felügyelt identitás Megjelenítés az ADF bináris formában.

7.  A lekérdezési ablakban törölje, és hajtsa végre a következő T-SQL-parancsfájl hozzáadása a felügyelt identitás az ADF felhasználóként

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID ={your SERVICE IDENTITY APPLICATION ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    A parancs sikeresen befejeződik, a felügyelt identitás biztosítása a az ADF-adatbázis létrehozása lehetővé teszi.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Az Azure portal/ADF-alkalmazás üzembe helyezése Azure-SSIS integrációs

Amikor üzembe helyezi az Azure portal/ADF-alkalmazásban, az Azure-SSIS integrációs a **SQL-beállítások** lapon jelölje be **az ADF használata AAD-hitelesítését a felügyelt identitással** lehetőséget. Az alábbi képernyőképen az SSISDB üzemeltetési Azure SQL Database-kiszolgáló integrációs modul beállításai láthatók. Az integrációs modul az SSISDB, futtató felügyelt példányon a **katalógus adatbázisokra vonatkozó szolgáltatási szint** és **engedélyezi az Azure-szolgáltatások hozzáférési** beállítások nem vonatkoznak, bár egyéb beállítások megegyeznek.

Egy Azure-SSIS integrációs modul létrehozásával kapcsolatos további információkért lásd: [Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Az Azure-SSIS integrációs modul beállításai](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Üzembe helyezése az Azure-SSIS integrációs modul a PowerShell-lel

A PowerShell-lel az Azure-SSIS integrációs modul létrehozásához tegye a következőket:

1.  Telepítés [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modul.

2.  A parancsfájlban ne állítson be `CatalogAdminCredential` paraméter. Példa:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
