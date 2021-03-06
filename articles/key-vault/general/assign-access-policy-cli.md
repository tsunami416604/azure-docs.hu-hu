---
title: Azure Key Vault hozzáférési szabályzat (CLI) társítása
description: Az Azure CLI használatával Key Vault hozzáférési szabályzatot rendelhet hozzá egy egyszerű szolgáltatáshoz vagy alkalmazás-identitáshoz.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 59ba81944ecdf4f2b6322f4298e61df33f5b1da8
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289180"
---
# <a name="assign-a-key-vault-access-policy"></a>Key Vault hozzáférési szabályzat kiosztása

A Key Vault hozzáférési szabályzat meghatározza, hogy egy adott szolgáltatásnév, azaz egy alkalmazás vagy felhasználói csoport különböző műveleteket hajthat végre Key Vault [titkokon](../secrets/index.yml), [kulcsokon](../keys/index.yml)és [tanúsítványokon](../certificates/index.yml). Hozzáférési szabályzatokat a [Azure Portal](assign-access-policy-portal.md), az Azure CLI (ez a cikk) vagy [Azure PowerShell](assign-access-policy-powershell.md)használatával rendelhet hozzá.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

A Azure Active Directory csoportok Azure CLI-vel történő létrehozásával kapcsolatos további információkért lásd az [ad Group Create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) és [az ad Group tag Add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Az Azure CLI konfigurálása és bejelentkezés

1. Az Azure CLI-parancsok helyi futtatásához telepítse az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)-t.
 
    Ha közvetlenül a felhőben szeretné futtatni a parancsokat, használja a [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Csak helyi CLI: Jelentkezzen be az Azure-ba a következő használatával `az login` :

    ```bash
    az login
    ```

    A `az login` parancs megnyit egy böngészőablakot a hitelesítő adatok összegyűjtéséhez, ha szükséges.

## <a name="acquire-the-object-id"></a>Az objektumazonosító beszerzése

Határozza meg annak az alkalmazásnak, csoportnak vagy felhasználónak az AZONOSÍTÓját, amelyhez hozzá szeretné rendelni a hozzáférési házirendet:

- Alkalmazások és egyéb egyszerű szolgáltatások: az az [ad SP List](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) paranccsal kérheti le az egyszerű szolgáltatásokat. Vizsgálja meg a parancs kimenetét annak a rendszerbiztonsági tag objektum-AZONOSÍTÓjának a meghatározásához, amelyhez a hozzáférési házirendet hozzá kívánja rendelni.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Csoportok: használja az az [ad Group List](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) parancsot, és az eredményt a `--display-name` paraméterrel szűrheti:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Felhasználók: használja az az [ad User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) parancsot, és adja át a felhasználó e-mail-címét a következő `--id` paraméterben:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Hozzáférési házirend kiosztása
    
A kívánt engedélyek hozzárendeléséhez használja az az Key [Vault set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancsot:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Cserélje le a- `<object-id>` t az egyszerű szolgáltatásnév objektum-azonosítójával.

`--secret-permissions` `--key-permissions` Az adott típusokhoz csak a, a és az `--certificate-permissions` engedélyek kiosztása szükséges. A, a és a engedélyezett értékei az az Key `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` [Vault set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) dokumentációjában találhatók.

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault biztonság: identitás-és hozzáférés-kezelés](overview-security.md#identity-and-access-management)
- [A kulcstartó védelme](secure-your-key-vault.md).
- [Azure Key Vault fejlesztői útmutató](developers-guide.md)
- [Azure Key Vault ajánlott eljárások](best-practices.md)