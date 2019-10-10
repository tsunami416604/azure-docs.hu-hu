---
title: Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz
description: Ez a cikk a Key Vault létrehozásával és konfigurálásával kapcsolatos lépéseket ismerteti Azure Disk Encryption
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7c3d70610f8b26af17c5117896f4654a175473d2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245239"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz

A Azure Disk Encryption a Azure Key Vault használatával vezérli és kezeli a lemezes titkosítási kulcsokat és titkokat.  További információ a kulcstartókkal kapcsolatban: [Bevezetés a Azure Key Vault használatába](../../key-vault/key-vault-get-started.md) és [a kulcstartó biztonságossá tétele](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. További részletekért lásd: [kulcstartó létrehozása és konfigurálása az Azure Disk Encryptionhoz az Azure ad-vel (előző kiadás)](disk-encryption-key-vault-aad.md) .

A Key Vault létrehozásához és konfigurálásához Azure Disk Encryption a következő három lépésből áll:

1. Ha szükséges, hozzon létre egy erőforráscsoportot.
2. Kulcstartó létrehozása. 
3. A Key Vault speciális hozzáférési házirendjeinek beállítása.

Ezeket a lépéseket az alábbi rövid útmutatók szemléltetik:

- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása Azure PowerShell](disk-encryption-cli-quickstart.md)

Ha szeretné, létrehozhat vagy importálhat egy kulcs-titkosítási kulcsot (KEK) is.

> [!Note]
> A cikkben ismertetett lépések a [Azure Disk Encryption előfeltételként megadott parancssori felület parancsfájljában](https://github.com/ejarvi/ade-cli-getting-started) és [Azure Disk Encryption előfeltételek PowerShell-parancsfájlban](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)találhatók.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és az Azure-hoz való kapcsolódás

A cikkben ismertetett lépések az [Azure CLI](/cli/azure/)-vel, az Azure PowerShell az [module](/powershell/azure/overview)vagy a [Azure Portal](https://portal.azure.com)használatával végezhetők el.

Amíg a portál elérhető a böngészőben, az Azure CLI és a Azure PowerShell helyi telepítést igényel; a részletekért lásd [: Azure Disk Encryption a Windows rendszerhez: eszközök telepítése](disk-encryption-windows.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy a Azure PowerShell használata előtt először csatlakoznia kell az Azure-előfizetéséhez. Ehhez [Jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Jelentkezzen be az Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)-lel, vagy adja meg a hitelesítő adatait a Azure Portalhoz, amikor a rendszer kéri.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Következő lépések

- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption forgatókönyvek megismerése Windows rendszerű virtuális gépeken](disk-encryption-windows.md)
- Tudnivalók a [Azure Disk Encryption hibakereséséről](disk-encryption-troubleshooting.md)
- A [Azure Disk Encryption minta parancsfájljainak](disk-encryption-sample-scripts.md) beolvasása
