---
title: Felügyelt identitás-hozzáférés hozzárendelése egy erőforráshoz az Azure CLI – Azure AD használatával
description: Lépésenkénti utasítások egy felügyelt identitás hozzárendeléséhez egy erőforráshoz, egy másik erőforráshoz való hozzáféréshez az Azure CLI használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b241ac223fd1eb9df2b0a914726d8f37df5f4d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547375"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Felügyelt identitás-hozzáférés hozzárendelése egy erőforráshoz az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást felügyelt identitással, a felügyelt identitás hozzáférést adhat egy másik erőforráshoz, ugyanúgy, mint bármely rendszerbiztonsági tag. Ebben a példában bemutatja, hogyan adhat egy Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport felügyelt identitás-hozzáférését egy Azure STORAGE-fiókhoz az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A CLI parancsfájlpéldák futtatásához három lehetőség közül választhat:
    - Használja az [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell segítségével a "Try It" gomb, található a jobb felső sarokban minden kódblokk.
    - [Telepítse az Azure CLI legújabb verzióját,](https://docs.microsoft.com/cli/azure/install-azure-cli) ha helyi CLI-konzolt szeretne használni. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás-hozzáférés hozzárendelése az RBAC segítségével egy másik erőforráshoz

Miután engedélyezte a felügyelt identitást egy Azure-erőforráson, például egy [Azure virtuális gépen](qs-configure-cli-windows-vm.md) vagy az Azure [virtuálisgép-méretezési csoportban:](qs-configure-cli-windows-vmss.md) 

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Használjon olyan fiókot, amely az Azure-előfizetéshez van társítva, amely nek a virtuális gép vagy a virtuálisgép-méretezési készletét szeretné telepíteni:

   ```azurecli-interactive
   az login
   ```

2. Ebben a példában egy Azure virtuális gép hozzáférést biztosítunk egy tárfiókhoz. Először is az [az erőforráslistát](/cli/azure/resource/#az-resource-list) használunk a myVM nevű virtuális gép egyszerű szolgáltatásának lekérni:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Egy Azure virtuálisgép-méretezési csoport esetén a parancs ugyanaz, kivéve itt, a "DevTestVMSS" nevű virtuálisgép-méretezési csoport egyszerű szolgáltatása:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Miután rendelkezik az egyszerű szolgáltatásazonosítóval, az [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) segítségével a "Reader" virtuálisgép-méretezési készletnek hozzáférést biztosít a "myStorageAcct" nevű tárfiókhoz:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- Felügyelt identitás engedélyezése egy Azure virtuális gépen, [lásd: Felügyelt identitások konfigurálása az Azure-erőforrások egy Azure virtuális gép az Azure CLI használatával.](qs-configure-cli-windows-vm.md)
- Felügyelt identitás engedélyezése egy Azure virtuálisgép-méretezési csoport, [lásd: Felügyelt identitások konfigurálása az Azure-erőforrások egy virtuális gép méretezési csoport az Azure CLI használatával.](qs-configure-cli-windows-vmss.md)
