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
ms.openlocfilehash: be80065f83e8c80e7c1d6ab383cea04e0d2679a0
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>A kezelt Service Identity (MSI) hozzáférés hozzárendelése egy erőforrás Azure parancssori felület használatával

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Egy Azure-erőforrás konfigurálása az egy olyan MSI Csomaghoz, után az MSI-hozzáférést biztosíthat más erőforráshoz, hasonlóan egy rendszerbiztonsági tagot. Ez a példa bemutatja, hogyan lehetőséget, ha egy Azure virtuális gép MSI hozzáférést az Azure storage-fiókhoz, az Azure parancssori felület használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

A parancssori felület parancsfájl példák futtatásához három lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](../cloud-shell/overview.md) Azure-portálról (lásd a következő szakaszt).
- A beágyazott Azure Cloud rendszerhéj a "próbálja" gombra, minden egyes kódblokk jobb felső sarkában található keresztül használja.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Az MSI-hozzáférés hozzárendelése egy másik erőforrás RBAC használata

Egy Azure-erőforrás a MSI bekapcsolását követően [például egy Azure virtuális gép](msi-qs-configure-cli-windows-vm.md): 

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/#az_login). Használjon olyan fiókot, amelybe szeretne telepíteni a virtuális Gépet az Azure-előfizetéshez társított:

   ```azurecli-interactive
   az login
   ```

2. Ebben a példában azt hozzáadásakor jogosultságot ad az Azure virtuális gép hozzáférés tárfiókba. Először használjuk [az erőforráslistát](/cli/azure/resource/#az_resource_list) a szolgáltatás egyszerű beolvasni a virtuális gép "myVM", amely jött létre, amikor a jelenleg engedélyezve van a virtuális Gépen az MSI-fájl neve:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```

3. Ha a résztvevő-azonosító van, hogy a [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az_role_assignment_create) ahhoz, hogy a virtuális gép megkapja a storage-fiókok hozzáférést "Olvasó" nevű "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az erőforrás MSI nem jelenik meg a rendelkezésre álló azonosítók listája, győződjön meg arról, hogy az MSI-fájl helytelenül engedélyezett. Ebben az esetben azt is visszatérhet az Azure virtuális gépen lévő a [Azure-portálon](https://portal.azure.com) és:

- Nézze meg a "Konfiguráció" lapot, és ellenőrizze az MSI engedélyezve = "Yes".
- Nézze meg a "Kiterjesztésekkel" lapot, és ellenőrizze az MSI-bővítmény sikeresen telepítve.

Vagy nem megfelelő, ha szeretne újra telepíteni a erőforráson MSI, vagy a központi telepítési hiba elhárítása.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).
- Egy Azure virtuális gépen az MSI engedélyezéséről [konfigurálása az Azure virtuális gép felügyelt szolgáltatás identitásának (MSI) Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.

