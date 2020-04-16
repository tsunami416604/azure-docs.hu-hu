---
title: 'Rövid útmutató: Kulcs beállítása és beolvasása az Azure Key Vaultból'
description: Rövid útmutató, amely bemutatja, hogyan állíthat be és kérhet be egy kulcsot az Azure Key Vaultból az Azure PowerShell használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: c407c10327a80de6b3df18a3db3978468c9f8da0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424187"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Rövid útmutató: Kulcs beállítása és lekérése az Azure Key Vaultból az Azure PowerShell használatával

Ebben a rövid útmutatóban hozzon létre egy key vault az Azure Key Vault az Azure PowerShell. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. A Key Vaultról további információt az [Áttekintés című témakörben talál.](../general/overview.md) Az Azure PowerShell segítségével hozhat létre és kezelhet Azure-erőforrásokat parancsok vagy parancsfájlok használatával. Amint ezt befejezte, tárolni fog egy kulcsot.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez az oktatóanyag az Azure PowerShell 1.0.0-s vagy újabb verzióját igényli. Írja `$PSVersionTable.PSVersion` be a verzió megkereséséhez. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup segítségével.](/powershell/module/az.resources/new-azresourcegroup) Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Következő lépésként létrehoz egy kulcstartót. Ehhez a lépéshez tudnia kell néhány dolgot:

Bár a "Contoso KeyVault2" a key vault neve ebben a rövid útmutatóban, egyedi nevet kell használnia.

- **Tároló neve**: Contoso-Vault2.
- **Erőforráscsoport neve**: ContosoResourceGroup.
- **Hely** Usa keleti része.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott kulcstartó tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

* **Tároló neve**: A példában ez **Contoso-Vault2**. Ezt a nevet fogja majd más Key Vault parancsmagokban is megadni.
* **A tároló URI-ja**: Ebben a példában ez https://Contoso-Vault2.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

A tároló létrehozása után az Azure-fiókja az egyetlen fiók, amely bármit végrehajthat az új tárolón.

![Kimenet a Key Vault létrehozási parancsának a befejeződése után](../media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="add-a-key-to-key-vault"></a>Kulcs hozzáadása a Key Vaulthoz

Kulcs hozzáadása a tárolóhoz, csak néhány további lépést kell tennie. Ezt a kulcsot egy alkalmazás is használhat. 

Az alábbi parancsok beírásával hozzon létre egy **ExampleKey nevű:**

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

Most már hivatkozhat erre a kulcsra, amelyet az URI használatával adott hozzá az Azure Key Vaulthoz. Az **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** aktuális verzió bekésezéséhez használható. 

A korábban tárolt kulcs megtekintése:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

Most létrehozott egy Key Vaultot, tárolt egy kulcsot, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault ot, és egy tanúsítványt tárolt benne. Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- Az [Azure Key Vault áttekintésének elolvasása](../general/overview.md)
- Tekintse meg az [Azure PowerShell Key Vault-parancsmagok hivatkozási!](/powershell/module/az.keyvault/)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](../general/best-practices.md)
