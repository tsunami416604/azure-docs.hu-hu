---
title: MSI-hozzáférés hozzárendelése egy Azure-erőforrás, Azure CLI használatával
description: Részletes utasítások hozzárendelése egy olyan MSI Csomaghoz, egy erőforráson, egy másik erőforrás, Azure CLI-vel való hozzáférést.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: a5da06eac7f4680282aad305f57cb9ca1c9d5730
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424432"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>A Felügyeltszolgáltatás-identitás (MSI) hozzáférés hozzárendelése egy erőforrást az Azure CLI használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurálta az Azure-erőforrás egy MSI-vel, az MSI hozzáférést biztosíthat egy másik erőforráshoz, csakúgy, mint egy rendszerbiztonsági tagot. Ez a példa bemutatja, hogyan biztosíthat az Azure virtuális gép vagy a virtuális gép méretezési csoportjának MSI-hozzáférés az Azure storage-fiókba, az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Három lehetősége van a CLI-példaszkriptek futtatásához:

- Használat [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
- Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
- [Telepítse a CLI 2.0 legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13-as vagy újabb) Ha inkább a helyi CLI-konzol használatával. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Az RBAC használatával az MSI-hozzáférés hozzárendelése egy másik erőforrás

Engedélyezését követően az MSI-Azure-erőforrás, például egy [Azure virtuális gép](qs-configure-cli-windows-vm.md) vagy [Azure-beli virtuálisgép-méretezési csoport](qs-configure-cli-windows-vmss.md): 

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Amelyben üzembe helyezése a virtuális gép vagy virtuálisgép-méretezési csoportot szeretne Azure-előfizetéssel társított fiókot használ:

   ```azurecli-interactive
   az login
   ```

2. Ebben a példában azt egy Azure-beli virtuálisgép-hozzáférés, hogy küldjön egy storage-fiókot. Először használjuk [az erőforrások listájából](/cli/azure/resource/#az-resource-list) az egyszerű szolgáltatás a virtuális gép "myVM" nevű beolvasásához:

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

## <a name="troubleshooting"></a>Hibaelhárítás

Az MSI az erőforrás nem jelenik meg a rendelkezésre álló identitások listájában, győződjön meg arról, hogy az MSI engedélyezve lett-e megfelelően. Ebben az esetben is visszatér az Azure virtuális gép vagy virtuálisgép-méretezési csoportját az [az Azure portal](https://portal.azure.com) és:

- Tekintse meg a "Konfiguráció" lapot, és ellenőrizze, engedélyezett MSI-vel = "Yes".
- Tekintse meg a "Bővítmények" lapot, és győződjön meg, hogy az MSI-bővítmény telepítése sikeresen (**bővítmények** lap nem érhető el egy Azure-beli virtuálisgép-méretezési csoportban).

Ha vagy helytelen, szükség lehet ismételt üzembe helyezése az MSI az erőforráson újra, vagy az üzemelő példány hibájának elhárítása.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](overview.md).
- Egy Azure virtuális gépen az MSI engedélyezéséhez tekintse [konfigurálni egy Azure virtuális gépek Felügyeltszolgáltatás-identitás (MSI) Azure parancssori felületével](qs-configure-cli-windows-vm.md).
- Egy Azure-beli virtuálisgép-méretezési csoportot az MSI engedélyezéséhez tekintse [konfigurálása az Azure virtuális gép méretezési beállítása Felügyeltszolgáltatás-identitás (MSI) az Azure portal használatával](qs-configure-portal-windows-vmss.md)

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.

