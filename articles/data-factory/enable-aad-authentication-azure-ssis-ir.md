---
title: Az Azure Active Directory-hitelesítés engedélyezése az Azure-SSIS integrációs modul |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan konfigurálhatja az Azure-SSIS integrációs modul az Azure Active Directory-hitelesítést használó kapcsolatok engedélyezése.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: d2000e626166304e92556e3c965df175a27046ad
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321067"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS integrációs modul az Azure Active Directory-hitelesítés engedélyezése

Ez a cikk bemutatja, hogyan hozható létre egy Azure-SSIS integrációs modul az Azure Data Factory-szolgáltatásidentitás. Használhatja az Azure Active Directory (Azure AD) hitelesítési azokat a felügyelt identitáshoz az Azure Data Factory SQL-hitelesítés helyett egy Azure-SSIS integrációs modul létrehozása.

A data Factory a felügyelt identitás kapcsolatos további információkért lásd: [Azure Data Factory-szolgáltatásidentitás](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Ha már létrehozott egy Azure-SSIS integrációs modul az SQL-hitelesítéssel, újra az integrációs modul az Azure AD-hitelesítés használatához a PowerShell-lel jelenleg nem lehet konfigurálni.

## <a name="enable-azure-ad-on-azure-sql-database"></a>Az Azure SQL Database az Azure AD engedélyezése

Az Azure SQL Database támogatja az adatbázis létrehozása az Azure AD-felhasználót. Ennek eredményeképpen az Active Directory-rendszergazda állítja be az Azure AD-felhasználó, és ezután jelentkezzen be, az SQL Server Management Studio (SSMS) használatával az Azure AD-felhasználót. Létrehozhat egy felhasználó számára az Azure AD-csoport ahhoz, hogy az integrációs modul létrehozása az SQL Server Integration Services (SSIS) katalógust a kiszolgálón.

### <a name="create-a-group-in-azure-ad-and-make-the-managed-identity-for-your-data-factory-a-member-of-the-group"></a>Hozzon létre egy csoportot az Azure ad-ben, és adja hozzá a felügyelt identitás a data Factory a csoport

Meglévő Azure AD-csoportot is használhat, de újat is létrehozhat az Azure AD PowerShell-lel.

1.  Telepítse a [az Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul.

2.  Jelentkezzen be a `Connect-AzureAD`, és futtassa a következő parancsot létrehozni a csoportot, és mentse azt egy változóban:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    A kimenet a következő példának, amely a változó értékét is megvizsgálja hasonlóan néz ki:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  A data Factory a felügyelt identitás hozzáadása a csoporthoz. Követheti [Azure Data Factory-szolgáltatásidentitás](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) beolvasni az egyszerű SZOLGÁLTATÁSIDENTITÁS azonosítója (például 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, de erre a célra ne használja a SZOLGÁLTATÁSIDENTITÁS Alkalmazásazonosítója).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Is megvizsgálhatja a csoporttagság később.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Az Azure SQL Database az Azure AD-hitelesítés engedélyezése

Is [konfigurálása az Azure AD-hitelesítés az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) az alábbi lépéseket követve:

1.  Az Azure Portalon válassza ki a **minden szolgáltatás** -> **SQL Server-kiszolgálók** a bal oldali navigációs sávon.

2.  Válassza ki az SQL Database az Azure AD-hitelesítés engedélyezni kell.

3.  Az a **beállítások** a panel, válassza ki a szakasz **Active Directory-rendszergazda**.

4.  A parancssávon válassza ki a **rendszergazda beállítása**.

5.  Válassza ki az Azure AD felhasználói fiók lehet végezni a kiszolgáló rendszergazdája, és válassza ki a **válassza ki.**

6.  A parancssávon válassza ki a **mentéséhez.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Az Azure AD-csoportot képviselő tartalmazott felhasználó létrehozása az adatbázisban

A következő lépéshez szükséges [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Indítsa el az SQL Server Management Studiót.

2.  Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg az SQL server nevét a a **kiszolgálónév** mező.

3.  Az a **hitelesítési** mezőben válassza **Active Directory - MFA-támogatással rendelkező univerzális**. (Is más két Active Directory hitelesítési típusok is használhatja. Lásd: [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).)

4.  Az a **felhasználónév** mezőben adja meg az Ön által beállított kiszolgálói rendszergazdaként – például az Azure AD-szolgáltatásfiók neve testuser@xxxonline.com.

5.  Válassza ki **Connect**. Fejezze be a bejelentkezést.

6.  Az a **Object Explorer**, bontsa ki a **adatbázisok** -> rendszer-adatbázisok mappát.

7.  A jobb gombbal válassza **fő** adatbázisra, majd válassza **új lekérdezés**.

8.  A lekérdezési ablakban adja meg a következő sort, és válassza ki **Execute** kattintson az eszköztár:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    A parancsnak sikeresen futnia kell, és létre kell hoznia a csoport tartalmazott felhasználóját.

9.  Törölje a lekérdezési ablakban, adja meg a következő sort, és válassza ki **Execute** kattintson az eszköztár:

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    A parancs sikeresen befejeződik, a felhasználó jegykiadó képes létrehozni az adatbázist.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példány az Azure AD engedélyezése

Az Azure SQL Database felügyelt példánya támogatja a közvetlenül az MSI-vel egy adatbázis létrehozása. Nem kell a data factory MSI csatlakozzon egy Active Directory-csoportba, vagy hozza létre a felhasználó MI.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database-managed-instance"></a>Az Azure SQL Database felügyelt példányát az Azure AD-hitelesítés engedélyezése

1.   Az Azure Portalon válassza ki a **minden szolgáltatás** -> **SQL Server-kiszolgálók** a bal oldali navigációs sávon.

1.   Válassza ki az SQL server az Azure AD-hitelesítés engedélyezni kell.

1.   Az a **beállítások** a panel, válassza ki a szakasz **Active Directory-rendszergazda**.

1.   A parancssávon válassza ki a **rendszergazda beállítása**.

1.   Válassza ki az Azure AD felhasználói fiók lehet végezni a kiszolgáló rendszergazdája, és válassza ki a **kiválasztása**.

1.   A parancssávon válassza ki a **mentése**.

### <a name="add-data-factory-msi-as-a-user-to-the-azure-sql-database-managed-instance"></a>A data factory MSI felhasználóként hozzá az Azure SQL Database felügyelt példánya

1.  Indítsa el az SQL Server Management Studiót.

2.  Jelentkezzen be egy SQL-rendszergazdai fiókkal vagy az Active Directory-rendszergazdai fiókkal.

3.  Az Object Explorerben bontsa ki az adatbázisokat -> rendszer-adatbázisok mappát.

4.  Kattintson a jobb gombbal a master adatbázisban, és válassza ki **új lekérdezés**.

5.  Kövesse a cikk [Azure Data Factory-szolgáltatásidentitás](data-factory-service-identity.md) beolvasni az egyszerű szolgáltatás IDENTITÁS alkalmazásazonosítóját. (Ne használja SZOLGÁLTATÁSIDENTITÁS azonosítója erre a célra.)

6.  A lekérdezési ablakban futtassa a következő szkriptet a bináris típusú, a SZOLGÁLTATÁSIDENTITÁS Alkalmazásazonosítója konvertálni:

    ```sql
    DECLARE @applicationId uniqueidentifier = {your service identity application id}
    select CAST(@applicationId AS varbinary)
    ```

7.  Az eredmény ablakból beszerezheti az értéket.

8.  A lekérdezési ablakban törölje, és futtassa a következő szkriptet:

    ```sql
    CREATE LOGIN [{MSI name}] FROM EXTERNAL PROVIDER with SID ={your service identity application id in binary type}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{MSI name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{MSI name}]
    ```

9.  A parancs futtatása sikeresen befejeződik.

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>A portálon az Azure-SSIS integrációs modul üzembe helyezése

Amikor üzembe helyezi a Azure-SSIS integrációs modul az Azure Portallal, a a **SQL-beállítások** lapon kattintson a "használata aad-ben az ADF azokat a felügyelt identitáshoz hitelesítés" lehetőséget. (A következő képernyőképen látható az integrációs modul az Azure SQL Database beállításai. A felügyelt példány használatával az integrációs modul a "Katalógus adatbázisokra vonatkozó szolgáltatási szint" tulajdonság nem érhető el; egyéb beállítások megegyeznek.)

Egy Azure-SSIS integrációs modul létrehozásával kapcsolatos további információkért lásd: [Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Az Azure-SSIS integrációs modul beállításai](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>A PowerShell-lel az Azure-SSIS integrációs modul üzembe helyezése

A PowerShell-lel az Azure-SSIS integrációs modul létrehozásához tegye a következőket:

1.  Telepítse a [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modul.

2.  A parancsfájlban ne állítson be a *CatalogAdminCredential* paraméter. Példa:

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
