---
title: "Az MSI-hozzáférés hozzárendelése egy Azure-erőforrás, Azure parancssori felület használatával"
description: "Lépésről lépésre utasítások hozzárendelése egy olyan MSI Csomaghoz, egy erőforráson hozzáférni más erőforráshoz, Azure parancssori felület használatával."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: 90a7ec3059b6905e4aa660f538c299f3e8dedaae
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>A kezelt Service Identity (MSI) hozzáférés hozzárendelése egy erőforrás Azure parancssori felület használatával

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Egy Azure-erőforrás konfigurálása az egy olyan MSI Csomaghoz, után az MSI-hozzáférést biztosíthat más erőforráshoz, hasonlóan egy rendszerbiztonsági tagot. Ez a példa bemutatja, hogyan adhat egy Azure virtuális gépet vagy virtuális gép méretezési készlet MSI hozzáférés az Azure-tárfiókot, Azure parancssori felület használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

A parancssori felület parancsfájl példák futtatásához három lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](../cloud-shell/overview.md) Azure-portálról (lásd a következő szakaszt).
- A beágyazott Azure Cloud rendszerhéj a "próbálja" gombra, minden egyes kódblokk jobb felső sarkában található keresztül használja.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Az MSI-hozzáférés hozzárendelése egy másik erőforrás RBAC használata

Engedélyezését MSI a egy Azure-erőforrás, például egy [Azure virtuális gép](msi-qs-configure-cli-windows-vm.md) vagy [Azure virtuálisgép-méretezési csoport](msi-qs-configure-cli-windows-vmss.md): 

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/#az_login). Használjon olyan fiókot, amelybe szeretne telepíteni a virtuális gép vagy virtuálisgép-méretezési csoport Azure-előfizetéssel társított:

   ```azurecli-interactive
   az login
   ```

2. Ebben a példában azt hozzáadásakor jogosultságot ad az Azure virtuális gép hozzáférjen a tárfiókhoz. Először használjuk [az erőforráslistát](/cli/azure/resource/#az_resource_list) "myVM" nevű virtuális gép az egyszerű szolgáltatás eléréséhez:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Egy Azure virtuálisgép-méretezési csoport számára a parancs itt kivételével azonos, a szolgáltatás egyszerű lekérése "DevTestVMSS" nevű virtuálisgép-méretezési csoport:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Ha a résztvevő-azonosító van, a [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az_role_assignment_create) a virtuális gép vagy virtuálisgép-méretezési "myStorageAcct" nevű tárfiók "Olvasó" hozzáférési beállítása:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az erőforrás MSI nem jelenik meg a rendelkezésre álló azonosítók listája, győződjön meg arról, hogy az MSI-fájl helytelenül engedélyezett. Ebben az esetben azt is visszatérhet az Azure virtuális gép vagy állítsa be virtuálisgép-méretezési a [Azure-portálon](https://portal.azure.com) és:

- Nézze meg a "Konfiguráció" lapot, és ellenőrizze az MSI engedélyezve = "Yes".
- Nézze meg a "Kiterjesztésekkel" lapot, és ellenőrizze az MSI-bővítmény sikeresen telepítve (**bővítmények** lap nem érhető el egy Azure virtuálisgép-méretezési csoport).

Vagy nem megfelelő, ha szeretne újra telepíteni a erőforráson MSI, vagy a központi telepítési hiba elhárítása.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).
- Azure virtuális géphez engedélyezheti a MSI [konfigurálása az Azure virtuális gép felügyelt szolgáltatás identitásának (MSI) Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md).
- Egy Azure virtuálisgép-méretezési csoport MSI engedélyezéséhez tekintse [konfigurálása az Azure virtuális gép méretezési beállítása felügyelt szolgáltatás Identity (MSI) az Azure portál használatával](msi-qs-configure-portal-windows-vmss.md)

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.

