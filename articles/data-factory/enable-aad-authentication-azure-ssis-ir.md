---
title: Azure Active Directory-hitelesítés engedélyezése az Azure-SSIS-integrációs futásidejű |} Microsoft Docs
description: Ez a cikk ismerteti az Azure Active Directory-hitelesítést használó kapcsolatok engedélyezése az Azure-SSIS integrációs futásidejű konfigurálása.
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
ms.openlocfilehash: 93d3e25957fb1f04400fa78423a5658d32f7d5fd
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749718"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Az Azure-SSIS-integrációs futásidejű Azure Active Directory-hitelesítés engedélyezése

Ez a cikk bemutatja, hogyan hozzon létre egy Azure-SSIS-IR Azure Data Factory identitás. Az Azure Active Directory (Azure AD-) hitelesítés a a kezelt Service identitás (MSI) az Azure-SSIS-integrációs futásidejű lehetővé teszi a Data Factory MSI SQL-hitelesítés helyett egy Azure-SSIS-integrációs futásidejű létrehozásához.

A Data Factory MSI kapcsolatos további információk: [Azure Data Factory szolgáltatásidentitás](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Ha már létrehozott egy Azure-SSIS-integrációs futásidejű SQL-hitelesítéshez, nem konfigurálja újra a PowerShell jelenleg az Azure AD-alapú hitelesítés használata az infravörös.

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>Hozzon létre egy csoportot az Azure ad-ben, és adja hozzá a Data Factory MSI a csoport

Egy meglévő Azure AD-csoportot, vagy hozzon létre egy új Azure AD PowerShell segítségével.

1.  Telepítse a [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul.

2.  Jelentkezzen be `Connect-AzureAD`, és a csoport létrehozása a következő parancsot, és mentse azt egy változóban:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    A kimenet a következő példának, amely megvizsgálja-e a változó értékét is néz ki:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  A Data Factory MSI felvétele a csoportba. Követésével [Azure Data Factory szolgáltatásidentitás](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity) az egyszerű szolgáltatás IDENTITÁSÁNAK azonosítót (például 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, de erre a célra használja a szolgáltatás IDENTITÁSÁNAK Alkalmazásazonosító).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Is ellenőrizheti a csoporttagság később.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Az Azure SQL Database az Azure AD engedélyezése

Az Azure SQL Database támogatja az adatbázis létrehozása az Azure AD-felhasználó. Ennek eredményeképpen az Azure AD-felhasználó állítja be az Active Directory-rendszergazda, és az SSMS használatával az Azure AD-felhasználó, majd jelentkezzen be. Ezután egy felhasználó az Azure AD-csoport ahhoz, hogy az SQL Server Integration Services (SSIS) katalógus létrehozása a kiszolgálón az infravörös hozhat létre.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Az Azure SQL Database az Azure AD-hitelesítés engedélyezése

Is [konfigurálása az Azure AD-alapú hitelesítés, az SQL-adatbázis](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) az alábbi lépéseket követve:

1.  Válassza ki az Azure-portálon **minden szolgáltatás** -> **SQL Server-kiszolgálók** a bal oldali navigációs sávon.

2.  Válassza ki az SQL-adatbázis engedélyezni kell az Azure AD-alapú hitelesítés.

3.  Az a **beállítások** részében találhatja, jelölje be **Active Directory-rendszergazda**.

4.  A parancssávon válassza **-rendszergazda beállítása**.

5.  Válassza ki a kiszolgáló rendszergazdája, és válassza ki egy Azure AD-felhasználói fiókot **kiválasztása.**

6.  A parancssávon válassza **mentéséhez.**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Egy felhasználó az adatbázisban, amely jelöli az Azure AD-csoport létrehozása

A következő lépéshez szükséges [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Indítsa el az SQL Server Management Studiót.

2.  Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg az SQL server nevét a a **kiszolgálónév** mező.

3.  Az a **hitelesítési** mezőben válassza **Active Directory - MFA-támogatással rendelkező univerzális**. (Is más két Active Directory hitelesítési típusok használhatók. Lásd: [konfigurálása és kezelése az SQL Database, a példány által felügyelt Azure Active Directory hitelesítési](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure).)

4.  Az a **felhasználónév** mezőbe írja be az Azure AD-fiókot, amely állítja be a kiszolgáló rendszergazdája – például neve testuser@xxxonline.com.

5.  Válassza ki **Connect**. A bejelentkezési folyamat elvégzése.

6.  Az a **Object Explorer**, bontsa ki a **adatbázisok** -> rendszer-adatbázisokat mappa.

7.  Jobb választás a **fő** adatbázisról, és válassza ki **új lekérdezés**.

8.  A lekérdezési ablakban írja be a következő sort, és válassza ki **Execute** az eszköztárban:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    A parancs sikeresen befejeződik, a felhasználó a csoport létrehozása.

9.  A lekérdezési ablakban törölje, írja be a következő sort, és válassza ki **Execute** az eszköztárban:

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    A parancs sikeresen befejeződik, a felhasználó képes létrehozni az adatbázis megadása.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Az Azure AD az Azure SQL adatbázis felügyelt példányon engedélyezése

Az Azure SQL adatbázis felügyelt példány nem támogatja az adatbázis létrehozása a bármely Azure AD-felhasználó nem AD a rendszergazdának. Ennek eredményeképpen kell az Azure AD-csoport állítja be az Active Directory-rendszergazdához. A felhasználó létrehozásához nem szükséges.

Is [konfigurálása az Azure AD-alapú hitelesítés az SQL-adatbázis felügyelt példány kiszolgáló](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) az alábbi lépéseket követve:

7.  Válassza ki az Azure-portálon **minden szolgáltatás** -> **SQL Server-kiszolgálók** a bal oldali navigációs sávon.

8.  Válassza ki az SQL server engedélyezni kell az Azure AD-alapú hitelesítés.

9.  Az a **beállítások** részében találhatja, jelölje be **Active Directory-rendszergazda**.

10. A parancssávon válassza **-rendszergazda beállítása**.

11. Keresés és az Azure AD-csoport (például SSISIrGroup) válassza ki és **kiválasztása.**

12. A parancssávon válassza **mentéséhez.**

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>A portálon az Azure-SSIS infravörös kiépítése

Amikor kiépítése az Azure-SSIS-IR és az Azure portál, az a **SQL-beállítások** lapon ellenőrizze a "AAD használja az ADF MSI hitelesítés" lehetőséget. (Az alábbi képernyőfelvételen látható az Azure SQL Database IR beállításait. Az IR-példánnyal felügyelt a "Katalógus adatbázis szolgáltatási rétegben" tulajdonság nem érhető el; egyéb beállításai megegyeznek.)

Egy Azure-SSIS-integrációs futásidejű létrehozásával kapcsolatos további információk: [hozzon létre egy Azure-SSIS-integrációs futásidejű Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime).

![Az Azure-SSIS-integrációs futásidejű beállításai](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>A PowerShell segítségével az Azure-SSIS infravörös kiépítése

A PowerShell segítségével az Azure-SSIS-IR kiépítéséhez, tegye a következőket:

1.  Telepítse a [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) modul.

2.  A parancsfájlban ne adja meg a *CatalogAdminCredential* paraméter. Példa:

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
