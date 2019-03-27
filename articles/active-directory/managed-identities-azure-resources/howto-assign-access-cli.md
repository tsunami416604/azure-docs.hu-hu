---
title: Egy felügyelt identitás hozzáférés hozzárendelése egy Azure-erőforrás Azure CLI használatával
description: Részletes utasításokat egy erőforráson, egy felügyelt identitás hozzárendelése egy másik erőforrás, Azure CLI-vel való hozzáférést.
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
ms.openlocfilehash: 2fb16b1762f1e5330cf058c37a6b7e0f008eb447
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443718"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Egy felügyelt identitás hozzáférések hozzárendelése az Azure CLI-vel erőforrás

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurálta az Azure-erőforrás felügyelt identitással, a felügyelt identitás hozzáférést biztosíthat más erőforráshoz, csakúgy, mint bármely rendszerbiztonsági tag. Ez a példa bemutatja, az Azure virtuális gép vagy a virtuális gép méretezési csoportjának felügyelt identitás hozzáférés biztosítása az Azure CLI-vel az Azure storage-fiókba.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használat [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Azure CLI legújabb verziójának telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) Ha inkább a helyi CLI-konzol használatával. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Az RBAC használatával hozzáférést egy felügyelt identitás hozzárendelése egy másik erőforrás

Engedélyezését követően felügyelt identitás egy Azure-erőforrás, például egy [Azure virtuális gép](qs-configure-cli-windows-vm.md) vagy [Azure-beli virtuálisgép-méretezési csoport](qs-configure-cli-windows-vmss.md): 

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Amelyben üzembe helyezése a virtuális gép vagy virtuálisgép-méretezési csoportot szeretne Azure-előfizetéssel társított fiókot használ:

   ```azurecli-interactive
   az login
   ```

2. Ebben a példában azt egy Azure-beli virtuálisgép-hozzáférés, hogy küldjön egy storage-fiókot. Először használjuk [az erőforrások listájából](/cli/azure/resource/#az-resource-list) a szolgáltatásnév lekérése a myVM nevű virtuális gépet:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Egy Azure virtuális gép méretezési csoport esetében a parancs kivéve itt, az egyszerű szolgáltatás megkapja a "DevTestVMSS" nevű virtuálisgép-méretezési csoportot:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Miután a szolgáltatásnév-Azonosítót, használhatja [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) biztosíthat a virtuális gép vagy virtuálisgép-méretezési csoport egy "myStorageAcct" nevű tárfiókot "Olvasó" hozzáférési beállítása:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások Azure-erőforrások – áttekintés](overview.md)
- Engedélyezheti a felügyelt identitás-beli virtuális gépen [konfigurálása felügyelt identitások az Azure-erőforrások egy Azure virtuális gépen az Azure CLI-vel](qs-configure-cli-windows-vm.md).
- Engedélyezheti a felügyelt identitás egy Azure-beli virtuálisgép-méretezési [konfigurálása felügyelt identitások az Azure-erőforrások egy virtuális gép méretezési csoportban Azure CLI-vel](qs-configure-cli-windows-vmss.md).
