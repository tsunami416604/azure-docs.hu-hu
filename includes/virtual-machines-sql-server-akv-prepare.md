---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 19be449528481b4e35cad4418f82f2250917966b
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
## <a name="prepare-for-akv-integration"></a>Készítse elő a AKV-integráció
Azure Key Vault-integráció segítségével konfigurálja az SQL Server virtuális gép, van néhány Előfeltételek: 

1. [Azure Powershell telepítése](#install)
2. [Hozzon létre egy Azure Active Directory](#register)
3. [Hozzon létre egy kulcstartót](#createkeyvault)

A következő szakaszok ismertetik ezeket az előfeltételeket és az adatokat kell összegyűjtenie később a PowerShell-parancsmagok futtatásához.

### <a id="install"></a> Azure PowerShell telepítése
Győződjön meg arról, hogy a legújabb Azure PowerShell SDK telepítése. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azureps-cmdlets-docs) foglalkozó témakörben talál.

### <a id="register"></a> Alkalmazás regisztrálása az Azure Active Directoryban

Először is szüksége van egy [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) az előfizetésben. Között számos előnyt Ez lehetővé teszi az egyes felhasználók és az alkalmazások a kulcstartót engedélyt szeretne megadni.

Ezután regisztrálni egy alkalmazás AAD-ben. Adja meg egy szolgáltatásnevet fiókot, amely hozzáfér a kulcstároló, amely a virtuális Gépre lesz szüksége. Az Azure Key Vault cikkben található lépéseket a a [alkalmazás regisztrálása az Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register) szakasz, vagy a lépéseket a képernyőképek látható a **identitás lekérése az alkalmazás szakaszban**  a [ebben a blogbejegyzésben](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Az alábbi lépések elvégzése előtt kell összegyűjtenie a következő adatokat, amelyek később szükség van az SQL virtuális gép az Azure Key Vault-integráció engedélyezése esetén a regisztráció során.

* Az alkalmazás hozzáadása után található a **Alkalmazásazonosító** a a **regisztrált alkalmazás** panelen.
    Az alkalmazás azonosítója hozzá van rendelve a később a **$spName** (egyszerű szolgáltatásnév) paraméter az Azure Key Vault-integráció engedélyezése a PowerShell-parancsfájlt.

   ![Alkalmazásazonosító](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Ezek a lépések során a kulcs létrehozásakor másolja a titkos kulcsot a kulcs az alábbi képernyőfelvételen látható módon. A kulcs titkos kulcs később a hozzá van rendelve a **$spSecret** (egyszerű titok) paraméterének a PowerShell-parancsfájlt.

   ![Az AAD titkos kulcs](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Az Alkalmazásazonosító és a titkos kulcsot is használható az SQL Server-hitelesítő adat létrehozása.

* Engedélyeznie kell az új ügyfél-Azonosítót a következő hozzáférési engedélyekkel rendelkezik: **titkosítása**, **visszafejtéséhez**, **wrapKey**, **unwrapKey**, **bejelentkezési**, és **ellenőrizze**. Ez a lépés a [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) parancsmag. További információkért lásd: [az alkalmazás használatához a kulcsok vagy titkos kulcsok](../articles/key-vault/key-vault-get-started.md#authorize).

### <a id="createkeyvault"></a> Hozzon létre egy kulcstartót
A virtuális Gépet a titkosításhoz használandó kulcsok tárolására az Azure Key Vault használatához szükséges kulcstároló elérésére. Ha már nem beállítása a kulcstároló, hozzon létre egyet a lépések a [Ismerkedés az Azure Key Vault](../articles/key-vault/key-vault-get-started.md) cikk. Az alábbi lépések elvégzése előtt van néhány információt kell összegyűjtenie a telepítéskor be később szükség van az SQL virtuális gép az Azure Key Vault-integráció engedélyezése esetén.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Amikor a hozzon létre egy kulcstartót lépés, vegye figyelembe a visszaadott **vaultUri** tulajdonságot, amelynek a kulcstároló URL-címét. Az alábbi ábrán látható, a kulcstároló neve ContosoKeyVault, adott lépésben megadott példa ezért a kulcstároló URL-cím lenne https://contosokeyvault.vault.azure.net/.

A kulcstároló URL-cím hozzá van rendelve a később a **$akvURL** paraméter az Azure Key Vault-integráció engedélyezése a PowerShell-parancsfájlt.

A key vault jön létre, igazolnia kell a hozzáad egy kulcsot a kulcstároló, ezt a kulcsot fog hivatkozni, amikor létrehozhatunk aszimmetrikus kulcsok létre kell hoznia az SQL Server később.
