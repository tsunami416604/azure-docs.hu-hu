---
title: Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz
description: Ez a cikk az Azure Disk Encryption szolgáltatással használható kulcstároló létrehozásának és konfigurálásának lépéseit ismerteti.
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 30fa6b910c0241621c2b2cdae9bb9a164f27cedb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454543"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz

Az Azure Disk Encryption az Azure Key Vault segítségével szabályozza és kezeli a lemeztitkosítási kulcsokat és titkos kulcsokat.  A kulcstartókról az [Azure Key Vault – és](../key-vault/key-vault-get-started.md) a Key Vault [biztonságossá tétele](../key-vault/general/secure-your-key-vault.md)című témakörben talál további információt.

Az Azure Disk Encryption szolgáltatással való használatra szolgáló kulcstartó létrehozása és konfigurálása három lépésből áll:

1. Erőforráscsoport létrehozása, ha szükséges.
2. Kulcstartó létrehozása. 
3. A key vault speciális hozzáférési szabályzatok beállítása.

Ezeket a lépéseket a következő rövid útmutatók szemléltetik:

Ha kívánja, kulcstitkosítási kulcsot (KEK) is létrehozhat vagy importálhat.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

A cikkben ismertetett lépések az [Azure CLI,](/cli/azure/)az [Azure PowerShell Az modul](/powershell/azure/overview)vagy az Azure [Portal](https://portal.azure.com)segítségével hajthatók végre.

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy az Azure PowerShell használata előtt először csatlakoznia kell az Azure-előfizetéshez. Ehhez jelentkezzen [be az Azure CLI-vel,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) [jelentkezzen be az Azure Powershellnel,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)vagy adja meg hitelesítő adatait az Azure Portalon, amikor a rendszer kéri.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>További lépések

- [Azure lemeztitkosítás – áttekintés](disk-encryption-overview.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure PowerShell használatával](disk-encryption-powershell.md)
