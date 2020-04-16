---
title: Az Azure Key Vault eléréséhez használjon rendszeráltal hozzárendelt felügyelt identitást
description: Megtudhatja, hogyan hozhat létre felügyelt identitást az App Service-alkalmazásokhoz, és hogyan használhatja azt az Azure Key Vault eléréséhez
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: bb5288d043ab5638bb33c357cea55c64b03fcf1d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432124"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>A Key Vault-hitelesítés biztosítása felügyelt identitással

Az Azure Active Directory felügyelt identitása lehetővé teszi, hogy az alkalmazás egyszerűen hozzáférhessen más Azure AD-vel védett erőforrásokhoz. Az identitás t an azure platform kezeli, és nem követeli meg, hogy kiépítse vagy forgassa a titkos kulcsokat. További információ: [Felügyelt identitások az Azure-erőforrásokhoz.](../../active-directory/managed-identities-azure-resources/overview.md) 

Ez a cikk bemutatja, hogyan hozhat létre egy felügyelt identitást egy App Service-alkalmazáshoz, és hogyan használhatja azt az Azure Key Vault eléréséhez. Az Azure-beli virtuális gépeken üzemeltetett alkalmazásokról a Windows virtuális gép rendszerhez rendelt felügyelt identitás használata az [Azure Key Vault eléréséhez.](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek 

Az útmutató végrehajtásához a következő erőforrásokkal kell rendelkeznie. 

- Egy kulcstartó. Használhatja a meglévő kulcstartót, vagy hozzon létre egy újat az alábbi rövid útmutatók egyikének lépéseit követve:
   - [Hozzon létre egy kulcstartót az Azure CLI-vel](../secrets/quick-create-cli.md)
   - [Kulcstartó létrehozása az Azure PowerShell használatával](../secrets/quick-create-powershell.md)
   - [Hozzon létre egy kulcstartót az Azure Portalon.](../secrets/quick-create-portal.md)
- Egy meglévő App Service-alkalmazás, amelyhez kulcstartó hozzáférést adhat. Az [App Service dokumentációjában](../../app-service/overview.md)leírt lépéseket követve gyorsan létrehozhat egyet.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview). Másik lehetőségként használhatja az [Azure Portalt.](https://portal.azure.com)


## <a name="adding-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása 

Először hozzá kell adnia egy rendszer-hozzárendelt identitást egy alkalmazáshoz. 
 
### <a name="azure-portal"></a>Azure Portal 

Felügyelt identitás beállítása a portálon, először hozzon létre egy alkalmazást a szokásos módon, majd engedélyezze a funkciót. 

1. Ha függvényalkalmazást használ, keresse meg a **Platform funkcióit.** Más alkalmazástípusok esetén görgessen le a **beállítások** csoporthoz a bal oldali navigációs sávon. 

1. Válassza **a Felügyelt identitás lehetőséget.** 

1. A **Rendszer hozzárendelt** lapján kapcsolja be az **Állapot szolgáltatást** **bekapcsolva**állásra. Kattintson a **Save** (Mentés) gombra. 

    ![](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziója szükséges. Az aktuális verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest) ismertető cikket. 

Az Azure CLI-vel való bejelentkezéshez használja az [az bejelentkezési](/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot:

```azurecli-interactive
az login
```

Az Azure CLI bejelentkezési beállításairól a Bejelentkezés az [Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)című témakörben talál további információt. 

Az alkalmazás identitásának létrehozásához használja az Azure CLI [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) parancsot vagy [az functionapp identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) parancsot:


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Jegyezze fel `PrincipalId`a , amely szükséges lesz a következő szakaszban.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Az alkalmazás hozzáférése a Key Vaulthoz 

### <a name="azure-portal"></a>Azure Portal

1.  Nyissa meg a Key Vault erőforrást. 

1.  Válassza **az Access-házirendek lehetőséget,** és kattintson **a Hozzáférési házirend hozzáadása gombra.** 

1.  A **Titkos engedélyek csoportban**válassza **a Get, List (Bekerülés idrét) lehetőséget.** 

1.  Válassza **az Egyszerű kijelölés lehetőséget,** és a keresőmezőbe írja be az alkalmazás nevét.  Jelölje ki az alkalmazást az eredménylistában, és kattintson a **Kijelölés gombra.** 

1.  Az új hozzáférési szabályzat hozzáadásának befejezéséhez kattintson a **Hozzáadás** gombra.

    ![](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Az alkalmazás hozzáférésének engedélyezéséhez a key vault, használja az Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancs, amely az **ObjectId** paraméter a fent említett **rendszeri tag.**

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>További lépések

- [Az Azure Key Vault biztonsága: Identitás- és hozzáférés-kezelés](overview-security.md#identity-and-access-management)
- [A Kulcstároló hitelesítésének biztosítása hozzáférés-vezérlési házirenddel](group-permissions-for-apps.md)
- [A kulcstartó biztosítása).](secure-your-key-vault.md)
- [Az Azure Key Vault fejlesztői útmutatója](developers-guide.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](best-practices.md)