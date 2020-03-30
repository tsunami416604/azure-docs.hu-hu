---
title: 'Rövid útmutató: Állítsa be & lekérni egy titkos kulcsot a Key Vaultból a PowerShell használatával'
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkokat egy Azure Key Vaultból az Azure PowerShell használatával.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 627d74f48c0f2b3da8665cd255102f36869477c2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79472758"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Rövid útmutató: Titkos kulcs beállítása és lekérése az Azure Key Vaultból a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. A Key Vaulttal kapcsolatos további információt az [Áttekintés](key-vault-overview.md) szakaszban talál. Ebben a rövid útmutatóban egy kulcstartót hoz létre a PowerShell használatával. Ezután titkos kulcsokat tárolhat az újonnan létrehozott tárolóban.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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

Bár ebben a rövid útmutatóban a kulcstartó neve Contoso KeyVault2, egyedi nevet kell használnia.

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

![Kimenet a Key Vault létrehozási parancsának a befejeződése után](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Titkos kulcs a tárolóhoz való hozzáadásához csak néhány lépést kell végrehajtania. Ebben az esetben egy alkalmazás által használható jelszót fog megadni. A jelszó neve **ExamplePassword,** és tárolja az értékét **hVFkk965BuUv** benne.

Először konvertálja a **hVFkk965BuUv** értékét biztonságos karakterláncúrrá a következő beírással:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Ezután írja be az alábbi PowerShell-parancsokat, hogy hozzon létre egy **példajelszó** nevű titkos kulcsot a Key Vaultban **hVFkk965BuUv** értékkel:

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword' -SecretValue $secretvalue
```

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contoso-Vault2" -name "ExamplePassword").SecretValueText
```

Most létrehozott egy Key Vaultot, tárolt egy titkos kulcsot, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

 A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a Key Vaultot és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault ot, és egy titkos titkot tárolt benne. Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- Az [Azure Key Vault áttekintésének elolvasása](key-vault-overview.md)
- Tekintse meg az [Azure PowerShell Key Vault-parancsmagok hivatkozási!](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault)
- Tudnivalók [a kulcsokról, titkos kulcsokról és tanúsítványokról](about-keys-secrets-and-certificates.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](key-vault-best-practices.md)
