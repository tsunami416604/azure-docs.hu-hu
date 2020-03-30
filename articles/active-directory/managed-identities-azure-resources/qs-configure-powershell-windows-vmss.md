---
title: Felügyelt identitások konfigurálása virtuálisgép-méretezési csoportokon a PowerShell – Azure AD használatával
description: Lépésenkénti útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálása egy virtuális gép méretezési csoport a PowerShell használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 755aee312fd0492fd57a82cb7a437b04ebf72987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547269"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Felügyelt identitások konfigurálása az Azure-erőforrásokhoz a PowerShell használatával a virtuálisgép-méretezési csoportokon

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben a PowerShell használatával megtudhatja, hogyan hajthatja végre az Azure-erőforrások műveleteinek felügyelt identitásait egy virtuálisgép-méretezési csoporton:
- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy virtuálisgép-méretezési csoportban
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása virtuálisgép-méretezési csoporton

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A jelen cikkben szereplő felügyeleti műveletek végrehajtásához a fióknak a következő Azure-szerepköralapú hozzáférés-vezérlési hozzárendelésekre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

    - [Virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) egy virtuálisgép-méretezési csoport létrehozásához, és engedélyezi és eltávolítja a rendszer által hozzárendelt felügyelt és/vagy a felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoportból.
    - [Felügyelt identitásközreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör a felhasználó által hozzárendelt felügyelt identitás létrehozásához.
    - [Felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör a felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez és eltávolításához egy virtuálisgép-méretezési csoporthoz.
- Telepítse [az Azure PowerShell legújabb verzióját,](/powershell/azure/install-az-ps) ha még nem tette meg. 

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és távolíthatja el a rendszer által hozzárendelt felügyelt identitást az Azure PowerShell használatával.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>A rendszeráltal hozzárendelt felügyelt identitás engedélyezése az Azure virtuálisgép-méretezési csoport létrehozása során

Virtuálisgép-méretezési csoport létrehozása a rendszer által hozzárendelt felügyelt identitással:

1. Az [Új AzVmssConfig-parancsmag](/powershell/module/az.compute/new-azvmssconfig) referenciacikk1. *Example 1*  Adja hozzá `-IdentityType SystemAssigned` a `New-AzVmssConfig` paramétert a parancsmaghoz:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése meglévő Azure virtuálisgép-méretezési csoporton

Ha engedélyeznie kell egy rendszer által hozzárendelt felügyelt identitást egy meglévő Azure virtuálisgép-méretezési csoporton:

1. Jelentkezzen be az `Connect-AzAccount`Azure-ba a használatával. Használjon egy fiókot, amely a virtuális gép méretezési készletét tartalmazó Azure-előfizetéshez van társítva. Győződjön meg arról is, hogy fiókja olyan szerepkörhöz tartozik, amely írási engedélyeket ad a virtuálisgép-méretezési csoporthoz, például a "Virtuálisgép közreműködő" készlethez:

   ```powershell
   Connect-AzAccount
   ```

2. Először olvassa be a virtuálisgép méretezési csoport tulajdonságait a [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) parancsmag használatával. Ezután a rendszer által hozzárendelt felügyelt `-IdentityType` identitás engedélyezéséhez használja az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag kapcsolóját:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás letiltása egy Azure virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési készletet használ, amelynek már nincs szüksége a rendszer által hozzárendelt felügyelt identitásra, de továbbra is felhasználó által hozzárendelt felügyelt identitásokra van szüksége, használja a következő parancsmast:

1. Jelentkezzen be az `Connect-AzAccount`Azure-ba a használatával. A virtuális gép tartalmazó Azure-előfizetéshez társított fiók használata. Győződjön meg arról is, hogy fiókja olyan szerepkörhöz tartozik, amely írási engedélyeket ad a virtuálisgép-méretezési csoporthoz, például a "Virtuálisgép közreműködő" készlethez:

2. Futtassa a következő parancsmagot:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Ha olyan virtuálisgép-méretezési készletet használ, amelynek már nincs szüksége rendszerhez rendelt felügyelt identitásra, és nem rendelkezik a felhasználó által hozzárendelt felügyelt identitásokkal, használja a következő parancsokat:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoportból az Azure PowerShell használatával.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure virtuálisgép-méretezési csoport létrehozása során

Egy új virtuálisgép-méretezési készlet létrehozása a felhasználó által hozzárendelt felügyelt identitás sal jelenleg nem támogatott a PowerShell által. Tekintse meg a következő szakaszt arról, hogyan adhat hozzá egy felhasználó által hozzárendelt felügyelt identitást egy meglévő virtuálisgép-méretezési csoporthoz. Térjen vissza frissítésekért.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure virtuálisgép-méretezési csoporthoz

Felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő Azure virtuálisgép-méretezési csoporthoz:

1. Jelentkezzen be az `Connect-AzAccount`Azure-ba a használatával. Használjon egy fiókot, amely a virtuális gép méretezési készletét tartalmazó Azure-előfizetéshez van társítva. Győződjön meg arról is, hogy fiókja olyan szerepkörhöz tartozik, amely írási engedélyeket ad a virtuálisgép-méretezési csoporthoz, például a "Virtuálisgép közreműködő" készlethez:

   ```powershell
   Connect-AzAccount
   ```

2. Először olvassa be a virtuálisgép méretezési csoport tulajdonságait a `Get-AzVM` parancsmag használatával. Ezután rendeljen hozzá egy felhasználó által hozzárendelt felügyelt identitást a virtuálisgép-méretezési csoporthoz, használja az `-IdentityType` `-IdentityID` [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmagés kapcsolja be. Cserélje `<VM NAME>` `<SUBSCRIPTION ID>`ki `<RESROURCE GROUP>` `<USER ASSIGNED ID1>`a `USER ASSIGNED ID2` , , , , a saját értékeit.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy Azure virtuálisgép-méretezési csoportból

Ha a virtuálisgép-méretezési csoport több felhasználó által hozzárendelt felügyelt identitással rendelkezik, az alábbi parancsokkal eltávolíthatja az utolsó kivételével az összeset. Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY NAME>` a felhasználó által hozzárendelt felügyelt identitás név tulajdonsága, amely nek meg kell maradnia a virtuális gép méretezési készlet. Ez az információ a virtuálisgép-méretezési csoport identitásszakaszában található a következő használatával: `az vmss show`

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Ha a virtuálisgép-méretezési csoport nem rendelkezik rendszeráltal hozzárendelt felügyelt identitással, és el szeretné távolítani belőle az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Ha a virtuálisgép-méretezési csoport rendszer- és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, eltávolíthatja az összes felhasználó által hozzárendelt felügyelt identitást, ha csak a rendszer által hozzárendelt felügyelt identitást használja.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- A teljes Azure virtuális gép létrehozása rövid útmutatók, lásd:
  
  - [Windows rendszerű virtuális gép létrehozása PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása PowerShell segítségével](../../virtual-machines/linux/quick-create-powershell.md) 

















