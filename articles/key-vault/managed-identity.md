---
title: Rendszerhez rendelt felügyelt identitás használata a Azure Key Vault eléréséhez
description: Ismerje meg, hogyan hozhat létre felügyelt identitást App Service-alkalmazásokhoz, és hogyan használhatja azt a Azure Key Vault eléréséhez
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 36a4871339401629300eedd77b6441aed10aabf3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199835"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Felügyelt identitással rendelkező Key Vault hitelesítés megadása

A Azure Active Directory felügyelt identitása lehetővé teszi, hogy az alkalmazás könnyedén hozzáférhessen más Azure AD-védelemmel ellátott erőforrásokhoz. Az identitást az Azure platform felügyeli, és nem igényli semmilyen titok kiépítését és elforgatását. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md). 

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy App Service alkalmazáshoz, és hogyan használhatja azt a Azure Key Vault eléréséhez. Az Azure-beli virtuális gépeken üzemeltetett alkalmazások esetében tekintse [meg a Windows rendszerű virtuális gépekhez rendelt felügyelt identitás használata a Azure Key Vault eléréséhez](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)című témakört


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek 

Az útmutató végrehajtásához a következő erőforrásokkal kell rendelkeznie. 

- Key Vault. Egy meglévő kulcstartót is használhat, vagy létrehozhat egy újat a következő rövid útmutatók lépéseinek végrehajtásával:
   - [Kulcstartó létrehozása az Azure CLI-vel](quick-create-cli.md)
   - [Key Vault létrehozása Azure PowerShell](quick-create-powershell.md)
   - [Hozzon létre egy Key vaultot a Azure Portal](quick-create-portal.md).
- Meglévő App Service alkalmazás, amelyhez kulcstartó-hozzáférést kíván biztosítani. A [app Service dokumentáció](../app-service/overview.md)lépéseinek követésével gyorsan létrehozhat egyet.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview). Azt is megteheti, hogy a [Azure Portal](https://portal.azure.com)is használja.


## <a name="adding-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása 

Először hozzá kell adnia egy rendszerhez rendelt identitást egy alkalmazáshoz. 
 
### <a name="azure-portal"></a>Azure Portal 

Ha felügyelt identitást szeretne beállítani a portálon, először hozzon létre egy alkalmazást a megszokott módon, majd engedélyezze a szolgáltatást. 

1. Ha Function alkalmazást használ, navigáljon a **platform szolgáltatásaihoz**. Más típusú alkalmazások esetén görgessen le a **Beállítások** csoportba a bal oldali navigációs sávon. 

1. Válassza a **felügyelt identitás**elemet. 

1. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva **értékre**. Kattintson a **Save** (Mentés) gombra. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Ehhez a rövid útmutatóhoz az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Az aktuális verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket. 

Az Azure CLI-vel való bejelentkezéshez használja az az [login](/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot:

```azurecli-interactive
az login
```

Az Azure CLI bejelentkezési lehetőségeivel kapcsolatos további információkért lásd: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Az alkalmazás identitásának létrehozásához használja az Azure CLI az [WebApp Identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) parancsot vagy az [az functionapp Identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) parancsot:


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Jegyezze fel a `PrincipalId`, amelyre szükség lesz a következő szakaszban.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Az alkalmazás hozzáférésének biztosítása Key Vault 

### <a name="azure-portal"></a>Azure Portal

1.  Navigáljon Key Vault erőforráshoz. 

1.  Válassza a **hozzáférési szabályzatok** lehetőséget, majd kattintson a **hozzáférési házirend hozzáadása**lehetőségre. 

1.  A **titkos engedélyek**területen válassza a **beolvasás,** majd a lista lehetőséget. 

1.  Válassza a **résztvevő kiválasztása**lehetőséget, majd a keresés mezőben adja meg az alkalmazás nevét.  Válassza ki az alkalmazást az eredmények listájában, majd kattintson a **kiválasztás**elemre. 

1.  Kattintson a **Hozzáadás** gombra az új hozzáférési szabályzat hozzáadásának befejezéséhez.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Ahhoz, hogy hozzáférést biztosítson az alkalmazáshoz a kulcstartóhoz, használja az Azure CLI az kulcstároló [set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancsot, és adja meg a **ObjectId** paramétert a fent említett **principalId** .

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>További lépések

- [Azure Key Vault biztonság: identitás-és hozzáférés-kezelés](overview-security.md#identity-and-access-management)
- [Hozzáférés-vezérlési házirenddel Key Vault hitelesítés megadása](key-vault-group-permissions-for-apps.md)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
- [A kulcstartó védelme](key-vault-secure-your-key-vault.md).
- [Azure Key Vault fejlesztői útmutató](key-vault-developers-guide.md)
- [Azure Key Vault ajánlott eljárások](key-vault-best-practices.md) áttekintése