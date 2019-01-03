---
title: A szolgáltatás egyszerű PowerShell-lel egy felügyelt identitás megtekintése
description: Részletes utasítások a szolgáltatásnév a PowerShell használatával egy felügyelt identitás megtekintéséhez.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0ad3a52b837a5f79c9976c4c509e0a8516de1e7d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714114"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>A szolgáltatás egyszerű PowerShell-lel egy felügyelt identitás megtekintése

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből elsajátíthatja a szolgáltatás egyszerű PowerShell-lel egy felügyelt identitás megtekintése.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nem rendelkezik Azure-fiók [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Engedélyezése [virtuális gépen a rendszer által hozzárendelt identitással](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazás](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. 
- Ha helyileg futtatja a PowerShellt, akkor emellett a következőket kell tennie: 
    - Futtassa a `Login-AzureRmAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.
    - Telepítse a [PowerShellGet legújabb verzióját](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Futtassa a következőt: `Install-Module -Name PowerShellGet -AllowPrerelease` a `PowerShellGet` modul kiadás előtti verziójának eléréséhez (előfordulhat, hogy a parancs futtatása után ki kell lépnie (`Exit`) az aktuális PowerShell-munkamenetből, hogy telepíteni tudja az `AzureRM.ManagedServiceIdentity` modult).
    - Futtatás `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` előzetes verziójának telepítése a `AzureRM.ManagedServiceIdentity` végrehajtásához, a felhasználó által hozzárendelt modul felügyelt identitás műveletek ebben a cikkben.

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

A következő parancs bemutatja, hogyan megtekintéséhez egy virtuális Géphez vagy alkalmazáshoz, az egyszerű szolgáltatás engedélyezve van a rendszer által hozzárendelt identitással. Cserélje le `<VM or application name>` a saját értékeire.

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>További lépések

A PowerShell-lel az Azure AD szolgáltatásnevek megtekintése további információkért lásd: [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal).


