---
title: MSI-hozzáférés hozzárendelése egy Azure-erőforrás, PowerShell-lel
description: Részletes utasítások hozzárendelése egy olyan MSI Csomaghoz, egy erőforráson, egy másik erőforrás, a PowerShell használatával való hozzáférést.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac8cca1e80defca33a879db5d4c160362314931a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610962"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>A Felügyeltszolgáltatás-identitás (MSI) hozzáférés hozzárendelése egy erőforrást PowerShell használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Miután konfigurálta az Azure-erőforrás egy MSI-vel, az MSI hozzáférést biztosíthat egy másik erőforráshoz, csakúgy, mint egy rendszerbiztonsági tagot. Ez a példa bemutatja, az Azure virtuális gép MSI-hozzáférés biztosítása az Azure storage-fiókba, PowerShell-lel.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Emellett telepítse [Azure PowerShell 4.3.1 verzió](https://www.powershellgallery.com/packages/AzureRM/4.3.1) Ha még nem tette.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Az RBAC használatával az MSI-hozzáférés hozzárendelése egy másik erőforrás

Miután engedélyezte az MSI-Azure-erőforrás [például egy Azure virtuális gép](msi-qs-configure-powershell-windows-vm.md):

1. Jelentkezzen be Azure-ban a `Connect-AzureRmAccount` parancsmagot. Egy fiókot, amely alatt az MSI konfigurálása Azure-előfizetéshez társított használja:

   ```powershell
   Connect-AzureRmAccount
   ```
2. Ebben a példában azt egy Azure-beli Virtuálisgép-hozzáférés, hogy küldjön egy storage-fiókot. Először használjuk [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) beolvasni az egyszerű szolgáltatás "myVM", amely jött létre, amikor lehetővé tettük az MSI nevű virtuális gép. Ezt követően használjuk [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) biztosíthat a virtuális gép "Olvasó" hozzáférést a tárfiókhoz "myStorageAcct" nevű:

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>Hibaelhárítás

Az MSI az erőforrás nem jelenik meg a rendelkezésre álló identitások listájában, győződjön meg arról, hogy az MSI engedélyezve lett-e megfelelően. Ebben az esetben is visszatér az Azure VM a [az Azure portal](https://portal.azure.com) és:

- Tekintse meg a "Konfiguráció" lapot, és ellenőrizze, engedélyezett MSI-vel = "Yes".
- Tekintse meg a "Bővítmények" lapot, és győződjön meg, hogy az MSI-bővítmény telepítése sikeresen.

Ha vagy helytelen, szükség lehet ismételt üzembe helyezése az MSI az erőforráson újra, vagy az üzemelő példány hibájának elhárítása.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).
- Egy Azure-beli virtuális gépen az MSI engedélyezéséhez tekintse [konfigurálása az Azure virtuális gépek Felügyeltszolgáltatás-identitás (MSI) PowerShell-lel](msi-qs-configure-powershell-windows-vm.md).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.

