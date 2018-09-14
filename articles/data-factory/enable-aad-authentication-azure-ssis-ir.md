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
ms.date: 06/21/2018
ms.author: douglasl
ms.openlocfilehash: aa06110a6f6fe668388c6aecd98c1ddeeae37edd
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576629"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS integrációs modul az Azure Active Directory-hitelesítés engedélyezése

Ez a cikk bemutatja, hogyan hozható létre egy Azure-SSIS integrációs modul az Azure Data Factory-szolgáltatásidentitás. Az Azure Active Directory (Azure AD-) hitelesítés a a Felügyeltszolgáltatás-identitás (MSI) az Azure-SSIS integrációs modul lehetővé teszi a Data Factory MSI használata az SQL-hitelesítés helyett egy Azure-SSIS integrációs modul létrehozásához.

A Data Factory MSI kapcsolatos további információkért lásd: [Azure Data Factory-szolgáltatásidentitás](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Ha már létrehozott egy Azure-SSIS integrációs modul az SQL-hitelesítéssel, újra az integrációs modul az Azure AD-hitelesítés használatához a PowerShell-lel jelenleg nem lehet konfigurálni.

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>Hozzon létre egy csoportot az Azure ad-ben, és adja hozzá a Data Factory MSI a csoport

Meglévő Azure AD-csoportot is használhat, de újat is létrehozhat az Azure AD PowerShell-lel.

1.  Telepítse a [az Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul.

2.  Jelentkezzen be a `Connect-AzureAD`, és futtassa a következő parancsot létrehozni a csoportot, és mentse azt egy változóban:

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

3.  A Data Factory MSI hozzáadása a csoporthoz. Követheti [Azure Data Factory-szolgáltatásidentitás](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) beolvasni az egyszerű SZOLGÁLTATÁSIDENTITÁS azonosítója (például 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, de erre a célra ne használja a SZOLGÁLTATÁSIDENTITÁS Alkalmazásazonosítója).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Is megvizsgálhatja a csoporttagság később.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Az Azure SQL Database az Azure AD engedélyezése

Az Azure SQL Database támogatja az adatbázis létrehozása az Azure AD-felhasználót. Ennek eredményeképpen az Active Directory-rendszergazda állítja be az Azure AD-felhasználó, és majd jelentkezzen be az SSMS használatával az Azure AD-felhasználót. Létrehozhat egy felhasználó számára az Azure AD-csoport ahhoz, hogy az integrációs modul létrehozása az SQL Server Integration Services (SSIS) katalógust a kiszolgálón.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Az Azure SQL Database az Azure AD-hitelesítés engedélyezése

Is [konfigurálása az Azure AD-hitelesítés az SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) az alábbi lépéseket követve:

1.  Az Azure Portalon válassza ki a **minden szolgáltatás** -> **SQL Server-kiszolgálók** a bal oldali navigációs sávon.

2.  Válassza ki az SQL Database az Azure AD-hitelesítés engedélyezni kell.

3.  Az a **beállítások** a panel, válassza ki a szakasz **Active Directory-rendszergazda**.

4.  A parancssávon válassza ki a **rendszergazda beállítása**.

5.  Válassza ki az Azure AD felhasználói fiók lehet végezni a kiszolgáló rendszergazdája, és válassza ki a **válassza ki.**

6.  A parancssávon válassza ki a **mentéséhez.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Az Azure AD-csoportot képviselő tartalmazott felhasználó létrehozása az adatbázisban

A következő lépéshez szükséges [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Indítsa el az SQL Server Management Studiót.

2.  Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg az SQL server nevét a a **kiszolgálónév** mező.

3.  Az **Authentication** (Hitelesítés) mezőben válassza ki az **Active Directory - Universal with MFA support** (Active Directory – univerzális, MFA-támogatással) lehetőséget. (Is más két Active Directory hitelesítési típusok is használhatja. Lásd: [konfigurálása és kezelése az Azure Active Directory-hitelesítés az SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).)

4.  Az a **felhasználónév** mezőben adja meg az Ön által beállított kiszolgálói rendszergazdaként – például az Azure AD-szolgáltatásfiók neve testuser@xxxonline.com.

5.  Válassza ki **csatlakozás**. Fejezze be a bejelentkezést.

6.  Az a **Object Explorer**, bontsa ki a **adatbázisok** -> rendszer-adatbázisok mappát.

7.  A jobb gombbal válassza **fő** adatbázisra, majd válassza **új lekérdezés**.

8.  A lekérdezési ablakban adja meg a következő sort, és válassza ki **Execute** kattintson az eszköztár:

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

Az Azure SQL Database felügyelt példánya nem támogatja a-adatbázis létrehozása az Azure AD-felhasználók eltérő AD-rendszergazdával. Ennek eredményeképpen kell az Azure AD-csoport állítja be az Active Directory-rendszergazdával. Hozzon létre a felhasználó nincs szükségünk.

Is [konfigurálása az Azure AD-hitelesítés az SQL Database felügyelt példányain kiszolgáló](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) az alábbi lépéseket követve:

7.  Az Azure Portalon válassza ki a **minden szolgáltatás** -> **SQL Server-kiszolgálók** a bal oldali navigációs sávon.

8.  Válassza ki az SQL server az Azure AD-hitelesítés engedélyezni kell.

9.  Az a **beállítások** a panel, válassza ki a szakasz **Active Directory-rendszergazda**.

10. A parancssávon válassza ki a **rendszergazda beállítása**.

11. Keresés és az Azure AD-csoport (például SSISIrGroup) válassza ki és **válassza ki.**

12. A parancssávon válassza ki a **mentéséhez.**

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>A portálon az Azure-SSIS integrációs modul üzembe helyezése

Amikor üzembe helyezi a Azure-SSIS integrációs modul az Azure Portallal, a a **SQL-beállítások** lapon kattintson a "használata AAD hitelesítési az ADF MSI-vel" lehetőséget. (A következő képernyőképen látható az integrációs modul az Azure SQL Database beállításai. A felügyelt példány használatával az integrációs modul a "Katalógus adatbázisokra vonatkozó szolgáltatási szint" tulajdonság nem érhető el; egyéb beállítások megegyeznek.)

Egy Azure-SSIS integrációs modul létrehozásával kapcsolatos további információkért lásd: [Azure-SSIS integrációs modul létrehozása az Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Az Azure-SSIS integrációs modul beállításai](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>A PowerShell-lel az Azure-SSIS integrációs modul üzembe helyezése

A PowerShell-lel az Azure-SSIS integrációs modul létrehozásához tegye a következőket:

1.  Telepítse a [Azure PowerShell-lel](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modul.

2.  A parancsfájlban ne állítson be a *CatalogAdminCredential* paraméter. Példa:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Type Managed `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -Description $AzureSSISDescription `
                                               -Edition $AzureSSISEdition `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
