---
title: Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz az Azure CLI-vel – Azure AD
description: Részletes útmutató egy felügyelt identitás egy erőforráshoz való hozzárendeléséhez, egy másik erőforráshoz való hozzáféréshez az Azure CLI használatával.
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74547375"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást egy felügyelt identitással, megadhatja a felügyelt identitás hozzáférését egy másik erőforráshoz, ugyanúgy, mint a rendszerbiztonsági tag. Ebből a példából megtudhatja, hogyan adhat egy Azure-beli virtuális gépet vagy virtuálisgép-méretezési csoport felügyelt identitás-hozzáférését egy Azure Storage-fiókhoz az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A CLI-szkriptek futtatásához a következő három lehetőség közül választhat:
    - Használja a Azure Portal [Azure Cloud shellt](../../cloud-shell/overview.md) (lásd a következő szakaszt).
    - A beágyazott Azure Cloud Shell az egyes kódrészletek jobb felső sarkában található "kipróbálás" gomb segítségével érheti el.
    - Ha inkább helyi CLI-konzolt szeretne használni, [telepítse az Azure CLI legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) . 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás-hozzáférés társítása egy másik erőforráshoz a RBAC használatával

Miután engedélyezte a felügyelt identitást egy Azure-erőforráson, például egy Azure-beli [virtuális gépen](qs-configure-cli-windows-vm.md) vagy egy Azure-beli [virtuálisgép-méretezési csoporton](qs-configure-cli-windows-vmss.md): 

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Olyan fiókot használjon, amely ahhoz az Azure-előfizetéshez van társítva, amelynek a virtuális gépet vagy virtuálisgép-méretezési csoportját szeretné telepíteni:

   ```azurecli-interactive
   az login
   ```

2. Ebben a példában egy Azure-beli virtuális gépet adunk hozzá egy Storage-fiókhoz. Először az [az Resource List](/cli/azure/resource/#az-resource-list) paranccsal szerezheti be a myVM nevű virtuális géphez tartozó szolgáltatásnevet:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Azure virtuálisgép-méretezési csoport esetén a parancs a "DevTestVMSS" nevű virtuálisgép-méretezési csoport egyszerű szolgáltatását kapja meg:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Ha már rendelkezik az egyszerű szolgáltatás azonosítójával, használja az [az role-hozzárendelés létrehozás](/cli/azure/role/assignment#az-role-assignment-create) lehetőséget, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport "olvasó" hozzáférhessen egy "mystorageacct kifejezést" nevű Storage-fiókhoz:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- Az Azure-beli virtuális gépek felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása](qs-configure-cli-windows-vm.md)Azure-beli virtuális gépen az Azure CLI használatával.
- Az Azure virtuálisgép-méretezési csoport felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása az Azure-erőforrásokhoz a virtuálisgép-méretezési csoportokban az Azure CLI használatával](qs-configure-cli-windows-vmss.md).
