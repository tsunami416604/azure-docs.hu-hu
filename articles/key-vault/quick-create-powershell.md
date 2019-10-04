---
title: Rövid Azure-útmutató – Titkos kulcs beállítása és lekérése a Key Vaultból a PowerShell használatával | Microsoft Docs
description: ''
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 5753384d12d31016dff7a33175899835c8bd1fec
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259262"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Gyors útmutató: Azure Key Vault titkos kód beállítása és beolvasása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. A Key Vaulttal kapcsolatos további információt az [Áttekintés](key-vault-overview.md) szakaszban talál. Ebben a rövid útmutatóban egy kulcstartót hoz létre a PowerShell használatával. Ezután titkos kulcsokat tárolhat az újonnan létrehozott tárolóban.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell-modul 1.0.0-as vagy újabb verziójára lesz szükség. A `$PSVersionTable.PSVersion` verzió megkereséséhez írja be a következőt:. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Következő lépésként létrehoz egy kulcstartót. Ehhez a lépéshez tudnia kell néhány dolgot:

Bár ebben a rövid útmutatóban a kulcstartó neve Contoso KeyVault2, egyedi nevet kell használnia.

- **Tároló neve**: Contoso-Vault2.
- **Erőforráscsoport neve**: ContosoResourceGroup.
- **Hely**: USA keleti régiója.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott kulcstartó tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

* Tár **neve**: A példában ez a **contoso-Vault2**. Ezt a nevet fogja majd más Key Vault parancsmagokban is megadni.
* Tároló **URI-ja**: Ebben a példában https://contosokeyvault.vault.azure.net/ ez a következő:. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

A tároló létrehozása után az Azure-fiókja az egyetlen fiók, amely bármit végrehajthat az új tárolón.

![Kimenet a Key Vault létrehozási parancsának a befejeződése után](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Titkos kulcs a tárolóhoz való hozzáadásához csak néhány lépést kell végrehajtania. Ebben az esetben egy alkalmazás által használható jelszót fog megadni. A jelszó neve **ExamplePassword** , és a **hVFkk965BuUv** értékét tárolja.

Először alakítsa át a **hVFkk965BuUv** értékét egy biztonságos karakterlánccá a következő beírásával:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Ezután írja be az alábbi PowerShell-parancsokat, és hozzon létre egy titkos kulcsot Key Vault nevű **ExamplePassword** a **hVFkk965BuUv** értékkel:

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Most létrehozott egy Key Vaultot, tárolt egy titkos kulcsot, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

 A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal távolíthatja el az erőforráscsoportot, Key Vault és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és egy titkos kulcsot tárolt benne. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](key-vault-overview.md)
- Tekintse meg a [Azure PowerShell Key Vault-parancsmagok](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault) referenciáját
- Tudnivalók a [kulcsokról, a titkokról és a tanúsítványokról](about-keys-secrets-and-certificates.md)
- [Azure Key Vault ajánlott eljárások](key-vault-best-practices.md) áttekintése
