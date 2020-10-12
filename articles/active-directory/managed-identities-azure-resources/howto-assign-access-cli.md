---
title: Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz az Azure CLI-vel – Azure AD
description: Részletes útmutató felügyelt identitások egy erőforráshoz való hozzárendeléséhez, egy másik erőforráshoz való hozzáféréshez az Azure CLI használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 13c69dda1e300bcff95b6a017fdeb308a6bbf3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969254"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást egy felügyelt identitással, megadhatja a felügyelt identitás hozzáférését egy másik erőforráshoz, ugyanúgy, mint a rendszerbiztonsági tag. Ebből a példából megtudhatja, hogyan adhat egy Azure-beli virtuális gépet vagy virtuálisgép-méretezési csoport felügyelt identitás-hozzáférését egy Azure Storage-fiókhoz az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A példaként szolgáló szkriptek futtatásához két lehetőség közül választhat:
    - Használja a [Azure Cloud shellt](../../cloud-shell/overview.md), amelyet a kódrészletek jobb felső sarkában található **kipróbálás** gomb használatával nyithat meg.
    - Futtassa helyileg a parancsfájlokat az [Azure CLI](/cli/azure/install-azure-cli)legújabb verziójának telepítésével, majd jelentkezzen be az Azure-ba az [az login](/cli/azure/reference-index#az-login)használatával. Használjon olyan Azure-előfizetéshez társított fiókot, amelyben erőforrásokat kíván létrehozni.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás-hozzáférés kiosztása az Azure RBAC használatával egy másik erőforráshoz

Miután engedélyezte a felügyelt identitást egy Azure-erőforráson, például egy Azure-beli [virtuális gépen](qs-configure-cli-windows-vm.md) vagy egy Azure-beli [virtuálisgép-méretezési csoporton](qs-configure-cli-windows-vmss.md): 

1. Ebben a példában egy Azure-beli virtuális gépet adunk hozzá egy Storage-fiókhoz. Először az [az Resource List](/cli/azure/resource/#az-resource-list) paranccsal szerezheti be a myVM nevű virtuális géphez tartozó szolgáltatásnevet:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Azure virtuálisgép-méretezési csoport esetén a parancs a "DevTestVMSS" nevű virtuálisgép-méretezési csoport egyszerű szolgáltatását kapja meg:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Ha már rendelkezik az egyszerű szolgáltatás azonosítójával, használja az [az role-hozzárendelés létrehozás](/cli/azure/role/assignment#az-role-assignment-create) lehetőséget, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport "olvasó" hozzáférhessen egy "mystorageacct kifejezést" nevű Storage-fiókhoz:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Következő lépések

- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- Az Azure-beli virtuális gépek felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása](qs-configure-cli-windows-vm.md)Azure-beli virtuális gépen az Azure CLI használatával.
- Az Azure virtuálisgép-méretezési csoport felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása az Azure-erőforrásokhoz a virtuálisgép-méretezési csoportokban az Azure CLI használatával](qs-configure-cli-windows-vmss.md).
