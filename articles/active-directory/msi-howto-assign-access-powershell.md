---
title: "Az MSI-hozzáférés hozzárendelése az Azure erőforráscsoport, a PowerShell használatával"
description: "Lépésről lépésre utasítások hozzárendelése egy olyan MSI Csomaghoz, egy erőforráson, egy másik erőforrás, a PowerShell használatával való hozzáférés."
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: bdc6268bd56598b917f51f39e604f858cfe81868
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>A kezelt Service Identity (MSI) hozzáférés hozzárendelése egy erőforrás PowerShell használatával

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Egy Azure-erőforrás konfigurálása az egy olyan MSI Csomaghoz, után az MSI-hozzáférést biztosíthat más erőforráshoz, hasonlóan egy rendszerbiztonsági tagot. Ez a példa bemutatja, hogyan lehetőséget, ha egy Azure virtuális gép MSI hozzáférést az Azure storage-fiókhoz, PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Továbbá, telepítse [Azure PowerShell verziója 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) Ha még nem tette meg.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Az MSI-hozzáférés hozzárendelése egy másik erőforrás RBAC használata

Egy Azure-erőforrás a MSI bekapcsolását követően [például egy Azure virtuális gép](msi-qs-configure-powershell-windows-vm.md):

1. Jelentkezzen be az Azure használatával a `Login-AzureRmAccount` parancsmag. Használjon olyan fiókot, amely alatt a MSI konfigurált Azure-előfizetéssel társított:

   ```powershell
   Login-AzureRmAccount
   ```
2. Ebben a példában azt hozzáadásakor jogosultságot ad az Azure virtuális gép hozzáférés tárfiókba. Először használjuk [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) a virtuális gép "myVM", amely jött létre, amikor a jelenleg engedélyezve van az MSI nevű az egyszerű szolgáltatás eléréséhez. Ezt követően használjuk [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) ahhoz, hogy a virtuális gép megkapja a storage-fiókok hozzáférést "Olvasó" nevű "myStorageAcct":

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az erőforrás MSI nem jelenik meg a rendelkezésre álló azonosítók listája, győződjön meg arról, hogy az MSI-fájl helytelenül engedélyezett. Ebben az esetben azt is visszatérhet az Azure virtuális gépen lévő a [Azure-portálon](https://portal.azure.com) és:

- Nézze meg a "Konfiguráció" lapot, és ellenőrizze az MSI engedélyezve = "Yes".
- Nézze meg a "Kiterjesztésekkel" lapot, és ellenőrizze az MSI-bővítmény sikeresen telepítve.

Vagy nem megfelelő, ha szeretne újra telepíteni a erőforráson MSI, vagy a központi telepítési hiba elhárítása.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).
- Egy Azure virtuális gépen az MSI engedélyezéséről [konfigurálása az Azure virtuális gép felügyelt szolgáltatás Identity (MSI) PowerShell használatával](msi-qs-configure-powershell-windows-vm.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.

