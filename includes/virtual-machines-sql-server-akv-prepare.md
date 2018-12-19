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
ms.openlocfilehash: 0a5d0f87b31652b1e1ab32c6b1594021937751b6
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046305"
---
## <a name="prepare-for-akv-integration"></a>Készítse elő az AKV-integráció
Az Azure Key Vault-integráció konfigurálása az SQL Server virtuális gép használatához van néhány előfeltételnek: 

1. [Az Azure Powershell telepítése](#install)
2. [Hozzon létre egy Azure Active Directory](#register)
3. [Kulcstartó létrehozása](#createkeyvault)

A következő szakaszok ismertetik ezeket az előfeltételeket és a információkat kell gyűjtenie később a PowerShell-parancsmagok futtatásához.

### <a id="install"></a> Az Azure PowerShell telepítése
Győződjön meg arról, hogy a legújabb Azure PowerShell SDK telepítése. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azureps-cmdlets-docs) foglalkozó témakörben talál.

### <a id="register"></a> Alkalmazás regisztrálása az Azure Active Directoryban

Először is szüksége lesz egy [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) az előfizetésében. Számos előnnyel jár, többek között ez lehetővé teszi, hogy adjon engedélyt a key vault egyes felhasználókat és alkalmazásokat.

Ezután regisztrálni egy alkalmazást az aad-ben. Ekkor kap egy egyszerű szolgáltatásfiókot, amely hozzáfér a kulcstartóra, amely a virtuális Gépre lesz szüksége. Az Azure Key Vault-cikkben szereplő lépéseket annak a [alkalmazás regisztrálása az Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register) szakaszt, és láthatja a képernyőképek a lépéseket a **identitás lekérése az alkalmazás szakaszban** , [ebben a blogbejegyzésben](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Az alábbi lépések elvégzése előtt kell gyűjtenie az alábbi információkat, amelyek később szükség van az SQL virtuális gép az Azure Key Vault-integráció engedélyezése esetén a regisztráció során.

* Az alkalmazás hozzáadása után keresse meg a **Alkalmazásazonosító** a a **regisztrált alkalmazás** panelen.
    Az Alkalmazásazonosítót később hozzá van rendelve a **$spName** (egyszerű szolgáltatásnév) paramétert az Azure Key Vault-integráció engedélyezése a PowerShell-parancsfájlt.

   ![Alkalmazásazonosító](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Ezek a lépések során a kulcs létrehozásakor másolja a titkos kulcs az alábbi képernyőképen látható módon. A kulcs titkos kód később hozzá van rendelve a **$spSecret** (egyszerű szolgáltatás titkos kódot) paramétert a PowerShell-parancsfájlt.

   ![AAD-secret](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Az Alkalmazásazonosító és a titkos kulcsot is használható az SQL Server-hitelesítő adat létrehozása.

* Engedélyeznie kell az új ügyfél-azonosító a következő hozzáférési engedélyekkel kell rendelkeznie: **első**, **wrapKey**, **unwrapKey**. Ez a lépés a [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) parancsmagot. További információkért lásd: [engedélyezése az alkalmazás számára a kulcs vagy titkos kód használatának](../articles/key-vault/key-vault-get-started.md#authorize).

### <a id="createkeyvault"></a> Kulcstartó létrehozása
Az Azure Key Vault tárolja a kulcsokat, a virtuális gép a titkosításhoz használandó, a key vault hozzáférésre van szüksége. Ha Ön még nem állította be a key vaultban, hozzon létre egyet a lépéseket követve a [Ismerkedés az Azure Key Vault](../articles/key-vault/key-vault-get-started.md) cikk. Az alábbi lépések elvégzése előtt van néhány információt kell gyűjtenie a telepítéskor be, amely később szükség van az SQL virtuális gép az Azure Key Vault-integráció engedélyezése esetén.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Amikor kap a létrehozása a key vault lépés, vegye figyelembe a visszaadott **vaultUri** tulajdonság, amely a key vault URL-címe. Ezt a lépést, a kulcstároló nevét ContosoKeyVault, az alább látható a megadott példa lecserélésére az tehát a key vault URL-cím lenne https://contosokeyvault.vault.azure.net/.

A key vault URL-cím később hozzá van rendelve a **$akvURL** paramétert az Azure Key Vault-integráció engedélyezése a PowerShell-parancsfájlt.

Után jön létre a key vault, a kulcs hozzáadása a key vaulthoz kell, ez a kulcs lehet hivatkozni, amikor létrehozunk egy aszimmetrikus kulcs létrehozni az SQL Server később.
