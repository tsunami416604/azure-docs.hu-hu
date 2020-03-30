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
ms.openlocfilehash: 2c7d312910c6d38c54b291da34bfb827246c7dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504402"
---
## <a name="prepare-for-akv-integration"></a>Felkészülés az AKV-integrációra
Az Azure Key Vault-integráció használatához az SQL Server virtuális gép konfigurálásához számos előfeltétel áll fenn: 

1. [Az Azure PowerShell telepítése](#install)
2. [Azure Active Directory létrehozása](#register)
3. [Kulcstartó létrehozása](#createkeyvault)

Az alábbi szakaszok ismertetik ezeket az előfeltételeket és a PowerShell-parancsmagok későbbi futtatásához szükséges adatokat.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a name="install-azure-powershell"></a><a id="install"></a>Az Azure PowerShell telepítése
Győződjön meg arról, hogy telepítette a legújabb Azure PowerShell-modult. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/install-az-ps) foglalkozó témakörben talál.

### <a name="register-an-application-in-your-azure-active-directory"></a><a id="register"></a>Alkalmazás regisztrálása az Azure Active Directoryban

Először rendelkeznie kell egy [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) az előfizetésben. Számos előnye, ez lehetővé teszi, hogy engedélyt adjon a key vault bizonyos felhasználók és alkalmazások számára.

Ezután regisztráljon egy alkalmazást az AAD-nél. Ez egy egyszerű szolgáltatásfiókot biztosít, amely hozzáfér a key vaulthoz, amelyre a virtuális gépnek szüksége lesz. Az Azure Key Vault cikkben ezeket a lépéseket az [Alkalmazás regisztrálása](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) az Azure Active Directoryval című szakaszban találja, vagy a képernyőképeket leadott lépéseket a [blogbejegyzés](https://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx) **alkalmazásának identitásának beszerezni című szakaszában** találja. A lépések végrehajtása előtt a regisztráció során a következő adatokat kell gyűjtenie, amelyekre később szükség van, amikor engedélyezi az Azure Key Vault-integrációt az SQL virtuális gépen.

* Az alkalmazás hozzáadása után keresse meg az **alkalmazásazonosítót** (más néven AAD ClientID vagy AppID) a **Regisztrált alkalmazás** panelen.
    Az alkalmazásazonosító később hozzá van rendelve a **$spName** (egyszerű szolgáltatásnév) paraméterhez a PowerShell-parancsfájlban az Azure Key Vault-integráció engedélyezéséhez.

   ![Alkalmazásazonosító](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* A kulcs létrehozásakor a következő lépések során másolja a kulcs titkos kulcsát az alábbi képernyőképen látható módon. Ez a kulcstitkos kulcs később hozzá van rendelve a **$spSecret** (egyszerű titkos) paraméterhez a PowerShell-parancsfájlban.

   ![AAD titok](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Az alkalmazásazonosító és a titkos adata is használható hitelesítő adatok létrehozásához az SQL Server ben.

* Az új alkalmazásazonosító (vagy ügyfélazonosító) engedélyezésével a következő hozzáférési engedélyekkel kell rendelkeznie: **get**, **wrapKey**, **unwrapKey**. Ez a [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmaggal történik. További információ: [Azure Key Vault overview](../articles/key-vault/key-vault-overview.md).

### <a name="create-a-key-vault"></a><a id="createkeyvault"></a>Kulcstartó létrehozása
Annak érdekében, hogy az Azure Key Vault használatával tárolja a titkosításhoz használt kulcsokat a virtuális gép, hozzáférésre van szüksége egy kulcstartóhoz. Ha még nem állította be a key vault, hozzon létre egyet az Azure Key Vault első [lépései](../articles/key-vault/key-vault-overview.md) című cikkben leírt lépéseket követve. A lépések végrehajtása előtt van néhány információ, amelyet a beállítás során kell gyűjtenie, amelyre később szükség van, amikor engedélyezi az Azure Key Vault-integrációt az SQL virtuális gépen.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Amikor eljut a Key Vault létrehozása lépés, vegye figyelembe a visszaadott **vaultUri** tulajdonság, amely a key vault URL-címét. A példában megadott lépésben, az alábbiakban látható, a key vault neve ContosoKeyVault, ezért a key vault URL-címe lenne. https://contosokeyvault.vault.azure.net/

A key vault URL-címe később van hozzárendelve a powershell-parancsfájl **$akvURL** paraméteréhez az Azure Key Vault-integráció engedélyezéséhez.

A key vault létrehozása után hozzá kell adnunk egy kulcsot a key vault, ez a kulcs lesz ajánlott, amikor létrehozunk egy aszimmetrikus kulcs létrehozása az SQL Server később.
